<div align="center">

# 🗳 ElectIQ
### India Election Process Education Platform

**Built for Hack2Skill Virtual PromptWars by Google**

![ElectIQ Banner](https://img.shields.io/badge/ElectIQ-Election%20Guide-EF9F27?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xOSAzSDVjLTEuMSAwLTIgLjktMiAydjE0YzAgMS4xLjkgMiAyIDJoMTRjMS4xIDAgMi0uOSAyLTJWNWMwLTEuMS0uOS0yLTItMnptLTcgM2MxLjEgMCAyIC45IDIgMnMtLjkgMi0yIDItMi0uOS0yLTIgLjktMiAyLTJ6bTQgMTJIOXYtMWMwLTEuMzMgMi42Ny0yIDQtMnM0IC42NyA0IDJ2MXoiLz48L3N2Zz4=)
![Version](https://img.shields.io/badge/version-1.0.0-blue?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-green?style=for-the-badge)
![Google Cloud](https://img.shields.io/badge/Google%20Cloud-Run-4285F4?style=for-the-badge&logo=googlecloud)
![Claude AI](https://img.shields.io/badge/Claude-AI%20Powered-orange?style=for-the-badge)

[🚀 Live Demo](#) · [📖 Documentation](#documentation) · [🐛 Report Bug](https://github.com/yourusername/electiq/issues)

</div>

---

## 📌 Overview

**ElectIQ** is an AI-powered, interactive web application that educates users about India's democratic election process — from the ECI announcement all the way to government formation. Built for the **Hack2Skill Virtual PromptWars by Google**, it combines structured civic education with live AI explanations powered by Claude.

---

## ✨ Features

| Feature | Description |
|--------|-------------|
| 🗺 **12-Step Pipeline** | Interactive step-by-step walkthrough of India's full election process |
| 🎛 **Phase Filter** | Filter steps by Pre-election, Nomination, Campaign, Polling, or Results phase |
| 📅 **Timeline View** | Expandable accordion timeline of key election stages |
| 🧠 **Randomized Quiz** | 20-question bank with 7 random questions per attempt, shuffled options |
| 🤖 **AI Assistant** | Live Claude-powered Q&A via `sendPrompt()` — zero CORS issues |
| 💡 **AI Quiz Hints** | Claude generates cryptic hints per question without revealing answers |
| 📱 **Responsive Design** | Works across desktop, tablet, and mobile screens |
| 🎨 **Civic Dark Theme** | Navy + saffron/gold palette reflecting India's national identity |

---

## 🏗 Architecture

```
electiq/
├── src/
│   └── index.html          # Main application (single-file)
├── public/
│   └── favicon.ico
├── docs/
│   ├── ARCHITECTURE.md
│   └── DEPLOYMENT.md
├── .github/
│   └── workflows/
│       └── deploy.yml      # Cloud Run auto-deploy
├── Dockerfile              # Container configuration
├── cloudbuild.yaml         # Google Cloud Build config
├── .dockerignore
├── package.json
└── README.md
```

---

## 🤖 AI Integration

ElectIQ uses **Claude AI** (Anthropic) for two features:

### 1. Ask AI Chat
- Powered by `sendPrompt()` inside Claude artifact environment
- For standalone deployment: route through a backend proxy
- Topic cards, quick questions, and custom input all supported
- Questions prefixed with `"ElectIQ question:"` for context

### 2. AI Quiz Hints
- Per-question cryptic hints requested via `sendPrompt()`
- System-prompted to never reveal the answer directly
- Appears inline in the quiz card

### Standalone Deployment Note
When deploying outside the Claude artifact environment, replace `sendPrompt()` calls with a backend API proxy:

```javascript
// Replace this:
sendPrompt('ElectIQ question: ' + text);

// With this (backend proxy):
const res = await fetch('/api/ask', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ question: text })
});
```

See `docs/DEPLOYMENT.md` for the full proxy server setup.

---

## 🐳 Docker Deployment

### Build locally
```bash
docker build -t electiq .
docker run -p 8080:8080 electiq
# Visit http://localhost:8080
```

### Deploy to Google Cloud Run
```bash
# One-command deploy
gcloud run deploy electiq \
  --source . \
  --region asia-south1 \
  --allow-unauthenticated \
  --platform managed
```

---

## ⚙️ Google Cloud Run Setup

### Prerequisites
```bash
# Install Google Cloud CLI
# https://cloud.google.com/sdk/docs/install

# Authenticate
gcloud auth login
gcloud config set project YOUR_PROJECT_ID

# Enable required APIs
gcloud services enable run.googleapis.com
gcloud services enable cloudbuild.googleapis.com
gcloud services enable containerregistry.googleapis.com
```

### Deploy with Cloud Build
```bash
gcloud builds submit --config cloudbuild.yaml
```

### Environment Variables (for AI proxy)
```bash
gcloud run services update electiq \
  --set-env-vars ANTHROPIC_API_KEY=your_key_here \
  --region asia-south1
```

---

## 📊 Quiz Bank

The app includes **20 election questions** covering:
- Constitutional provisions (Articles 324, etc.)
- EVM and VVPAT technology
- Election Commission powers
- Voting eligibility and process
- Campaign rules and spending limits
- Result declaration and government formation

7 questions are randomly selected per attempt with shuffled answer options.

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Vanilla HTML5, CSS3, JavaScript (ES6+) |
| Fonts | Google Fonts — Cormorant Garamond + Outfit |
| AI | Claude (Anthropic) via sendPrompt / API |
| Container | Docker + Nginx |
| CI/CD | GitHub Actions + Google Cloud Build |
| Hosting | Google Cloud Run |

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

**Built with ❤️ for Hack2Skill Virtual PromptWars by Google**

*Empowering civic education through AI*

</div>
