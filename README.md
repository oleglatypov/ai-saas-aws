# SaaS – Building a Full-Stack AI Application

> A complete example SaaS: **Next.js (Pages Router) + TypeScript + Tailwind** on the frontend, **FastAPI (Python)** on the backend, all **deployed to Vercel** with real-time streaming and Markdown rendering.

<p align="center">
  <a href="#-quickstart">Quickstart</a> •
  <a href="#-tech-stack">Tech Stack</a> •
  <a href="#-project-structure">Structure</a> •
  <a href="#-setup-frontend-nextjs">React/Next.js Setup</a> •
  <a href="#-setup-backend-fastapi">FastAPI Setup</a> •
  <a href="#-streaming--markdown">Streaming & Markdown</a> •
  <a href="#-styling">Styling</a> •
  <a href="#-deployment-vercel">Vercel Deployment</a> •
  <a href="#-troubleshooting">Troubleshooting</a>
</p>

---

## 🎯 What You’ll Build

A **Business Idea Generator** — an AI-powered SaaS application that:

- ✅ Modern React frontend with **Next.js (Pages Router)** and **TypeScript**
- ✅ **FastAPI** backend in Python
- ✅ **Real-time streaming** responses (SSE)
- ✅ Beautiful **Markdown rendering**
- ✅ **One-click deployment to Vercel**
- ✅ Clean, professional **Tailwind** UI

---

## 🧰 Prerequisites

- Node.js and **Vercel CLI** installed
- An **OpenAI API key**
- Python 3.10+ recommended

---

## 🚀 Quickstart

### 1) Clone & Install

```bash
# Clone your repo (or start fresh)
git clone <YOUR_REPO_URL> saas
cd saas

# Install frontend deps
npm install
```

### 2) Environment Variable

Add your OpenAI key to Vercel (works for local + deploys):

```bash
vercel env add OPENAI_API_KEY
```

> Paste your key when prompted. Choose **development**, **preview**, and **production**.

### 3) Link to Vercel

```bash
vercel link
```

- Set up & link? → **Yes**
- Scope → **Personal** (or your team)
- Link to existing? → **No**
- Project name → `saas`
- Code directory → **Current**

### 4) Deploy (Preview)

```bash
vercel .
```

Open the generated URL and you should see **Business Idea Generator** streaming ideas.

---

## 🧱 Tech Stack

- **Frontend:** Next.js (Pages Router), TypeScript, Tailwind CSS, React Markdown
- **Backend:** FastAPI (Python), Uvicorn
- **AI:** OpenAI Chat Completions
- **Deployment:** Vercel (Next.js + Python serverless)

---

## 🗂 Project Structure

```
saas/
├── api/                 # Python FastAPI server (Vercel serverless)
│   └── index.py
├── pages/               # Next.js Pages Router
│   ├── _app.tsx
│   ├── _document.tsx
│   └── index.tsx
├── public/              # Static assets
├── styles/
│   └── globals.css      # Tailwind + custom base styles
├── requirements.txt     # Python deps
├── package.json
├── tsconfig.json
└── next.config.js
```

---

## ⚛️ Setup (Frontend: Next.js)

Create a Next.js app with TypeScript and Tailwind (Pages Router):

```bash
npx create-next-app@latest saas --typescript
```

Choose:
1. **ESLint** → Enter
2. **Tailwind CSS** → `y`
3. **Use `src/`** → `n`
4. **App Router** → `n` (we’re using Pages Router)
5. **Turbopack** → `n`
6. **Import alias** → `n`


## 🔁 Git & GitHub Integration

Initialize and push:

```bash
git init
git add .
git commit -m "feat: initial SaaS app (Next.js + FastAPI)"
git branch -M main
git remote add origin <YOUR_REPO_URL>
git push -u origin main
```

Enable Vercel Git integration (optional but recommended):

- Connect your GitHub repo in the Vercel dashboard.
- Every push to `main` (or PR) will build a preview.
- Use **Environment Variables** in Vercel Project Settings.

---

## 🧩 Understanding Pages Router

- `pages/index.tsx` → `/`
- Each file in `pages/` becomes a route.
- We use **client-side components** (`"use client"`) to fetch from the **Python FastAPI** backend and stream results in real time.

---

## 🧭 Next Steps

- Add a “Generate New Idea” button
- Persist ideas to a database
- Add user auth
- Categorize ideas
- Copy-to-clipboard
- Share links

---

## 🛠 Troubleshooting

**Module not found**
- Ensure packages are installed: `npm install`
- If needed: delete `node_modules` + `package-lock.json`, then `npm install`

**API not responding**
- Confirm `OPENAI_API_KEY` exists in Vercel env for all environments

**Streaming not working**
- Check browser console
- Some browsers limit SSE on localhost — test the deployed preview

**ESLint warnings**
- Warnings don’t block builds. For simple demos you may ignore some `useEffect` deps warnings.

**TypeScript errors**
- Ensure types are installed; restart dev server after adding new packages

**Deployment issues**
- Save all files before deploying
- Confirm env vars in Vercel
