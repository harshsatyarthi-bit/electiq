# ElectIQ — Deployment Guide

## Quick Deploy (3 commands)

```bash
git clone https://github.com/harshsatyarthi-bit/electiq.git
cd electiq
gcloud run deploy electiq --source . --region asia-south1 --allow-unauthenticated
```

---

## Prerequisites

1. **Google Cloud account** with billing enabled
2. **Google Cloud CLI** installed: https://cloud.google.com/sdk/docs/install
3. **Docker** installed (for local testing)

---

## Step-by-Step Cloud Run Deployment

### 1. Set up Google Cloud project
```bash
gcloud auth login
export PROJECT_ID="your-project-id"
gcloud config set project $PROJECT_ID

# Enable APIs
gcloud services enable run.googleapis.com cloudbuild.googleapis.com containerregistry.googleapis.com
```

### 2. Build and deploy
```bash
# Option A: Direct source deploy (simplest)
gcloud run deploy electiq \
  --source . \
  --region asia-south1 \
  --allow-unauthenticated \
  --memory 256Mi

# Option B: Via Cloud Build
gcloud builds submit --config cloudbuild.yaml
```

### 3. Get your live URL
```bash
gcloud run services describe electiq \
  --region asia-south1 \
  --format='value(status.url)'
```

---

## GitHub Actions CI/CD Setup

### Required Secrets
Add these to GitHub → Settings → Secrets → Actions:

| Secret | Value |
|--------|-------|
| `GCP_PROJECT_ID` | Your Google Cloud project ID |
| `GCP_SA_KEY` | Service account JSON key (base64) |

### Create Service Account
```bash
# Create service account
gcloud iam service-accounts create electiq-deployer \
  --display-name="ElectIQ Deployer"

# Grant permissions
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:electiq-deployer@$PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/run.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:electiq-deployer@$PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:electiq-deployer@$PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/iam.serviceAccountUser"

# Download key and add to GitHub secrets
gcloud iam service-accounts keys create key.json \
  --iam-account="electiq-deployer@$PROJECT_ID.iam.gserviceaccount.com"

cat key.json | base64  # Copy this as GCP_SA_KEY secret
```

---

## Local Development

```bash
# Run locally with Node
npm install
npm run dev
# Visit http://localhost:3000

# Run with Docker
npm run docker:build
npm run docker:run
# Visit http://localhost:8080
```

---

## AI Integration for Standalone Deployment

The `sendPrompt()` function works only inside the Claude artifact environment.
For standalone Cloud Run deployment, use this Express.js proxy:

```javascript
// server.js — Add to your deployment
const express = require('express');
const Anthropic = require('@anthropic-ai/sdk');

const app = express();
const client = new Anthropic({ apiKey: process.env.ANTHROPIC_API_KEY });

app.use(express.json());
app.use(express.static('src'));

app.post('/api/ask', async (req, res) => {
  try {
    const message = await client.messages.create({
      model: 'claude-haiku-4-5-20251001',
      max_tokens: 400,
      system: 'You are ElectIQ, an expert on India\'s democratic election process. Plain prose only, under 100 words, no markdown.',
      messages: [{ role: 'user', content: req.body.question }]
    });
    res.json({ answer: message.content[0].text });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(process.env.PORT || 8080);
```

Then in `src/index.html`, replace `sendPrompt()` calls:
```javascript
// Before (Claude artifact only):
sendPrompt('ElectIQ question: ' + text);

// After (standalone):
const res = await fetch('/api/ask', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ question: text })
});
const { answer } = await res.json();
// Display answer in your UI
```
