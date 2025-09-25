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

- Node.js and **Vercel CLI** installed (from Day 1)
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

Clean up default API:

- Delete the `pages/api` folder (we use Python/fastapi instead).

**Create/replace** `pages/_app.tsx`:

```ts
import type { AppProps } from 'next/app';
import '../styles/globals.css';

export default function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />;
}
```

**Create** `pages/_document.tsx`:

```ts
import { Html, Head, Main, NextScript } from 'next/document';

export default function Document() {
  return (
    <Html lang="en">
      <Head>
        <title>Business Idea Generator</title>
        <meta name="description" content="AI-powered business idea generation" />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  );
}
```

**Create/replace** `pages/index.tsx` (basic version):

```ts
"use client"

import { useEffect, useState } from 'react';

export default function Home() {
  const [idea, setIdea] = useState<string>('…loading');

  useEffect(() => {
    fetch('/api')
      .then(res => res.text())
      .then(setIdea)
      .catch(err => setIdea('Error: ' + err.message));
  }, []);

  return (
    <main className="p-8 font-sans">
      <h1 className="text-3xl font-bold mb-4">Business Idea Generator</h1>
      <div className="w-full max-w-2xl p-6 bg-white dark:bg-gray-800 border border-gray-300 dark:border-gray-600 rounded-lg shadow-sm">
        <p className="text-gray-900 dark:text-gray-100 whitespace-pre-wrap">{idea}</p>
      </div>
    </main>
  );
}
```

---

## 🐍 Setup (Backend: FastAPI)

**Create** `api/` folder and add `api/index.py`:

```python
from fastapi import FastAPI  # type: ignore
from fastapi.responses import PlainTextResponse  # type: ignore
from openai import OpenAI  # type: ignore

app = FastAPI()

@app.get("/api", response_class=PlainTextResponse)
def idea():
    client = OpenAI()
    prompt = [{"role": "user", "content": "Come up with a new business idea for AI Agents"}]
    response = client.chat.completions.create(model="gpt-5-nano", messages=prompt)
    return response.choices[0].message.content
```

**Create** `requirements.txt`:

```
fastapi
uvicorn
openai
```

> Vercel will auto-detect and deploy Python serverless functions from `/api`.

---

## 📡 Streaming & Markdown

Install libraries:

```bash
npm install react-markdown remark-gfm remark-breaks @tailwindcss/typography
```

**Update** `api/index.py` to stream SSE:

```python
from fastapi import FastAPI  # type: ignore
from fastapi.responses import StreamingResponse  # type: ignore
from openai import OpenAI  # type: ignore

app = FastAPI()

@app.get("/api")
def idea():
    client = OpenAI()
    prompt = [{"role": "user", "content": "Reply with a new business idea for AI Agents, formatted with headings, sub-headings and bullet points"}]
    stream = client.chat.completions.create(model="gpt-5-nano", messages=prompt, stream=True)

    def event_stream():
        for chunk in stream:
            text = chunk.choices[0].delta.content
            if text:
                for line in text.split("\n"):
                    yield f"data: {line}\n"
                yield "\n"

    return StreamingResponse(event_stream(), media_type="text/event-stream")
```

**Enhance** `pages/index.tsx` to render Markdown & stream:

```ts
"use client"

import { useEffect, useState } from 'react';
import ReactMarkdown from 'react-markdown';
import remarkGfm from 'remark-gfm';
import remarkBreaks from 'remark-breaks';

export default function Home() {
  const [idea, setIdea] = useState<string>('…loading');

  useEffect(() => {
    const evt = new EventSource('/api');
    let buffer = '';

    evt.onmessage = (e) => {
      buffer += e.data;
      setIdea(buffer);
    };
    evt.onerror = () => {
      console.error('SSE error, closing');
      evt.close();
    };

    return () => { evt.close(); };
  }, []);

  return (
    <main className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 dark:from-gray-900 dark:to-gray-800">
      <div className="container mx-auto px-4 py-12">
        <header className="text-center mb-12">
          <h1 className="text-5xl font-bold bg-gradient-to-r from-blue-600 to-indigo-600 bg-clip-text text-transparent mb-4">
            Business Idea Generator
          </h1>
          <p className="text-gray-600 dark:text-gray-400 text-lg">
            AI-powered innovation at your fingertips
          </p>
        </header>

        <div className="max-w-3xl mx-auto">
          <div className="bg-white dark:bg-gray-800 rounded-2xl shadow-xl p-8 backdrop-blur-lg bg-opacity-95">
            {idea === '…loading' ? (
              <div className="flex items-center justify-center py-12">
                <div className="animate-pulse text-gray-400">
                  Generating your business idea...
                </div>
              </div>
            ) : (
              <div className="markdown-content text-gray-700 dark:text-gray-300">
                <ReactMarkdown remarkPlugins={[remarkGfm, remarkBreaks]}>
                  {idea}
                </ReactMarkdown>
              </div>
            )}
          </div>
        </div>
      </div>
    </main>
  );
}
```

---

## 🎨 Styling

Tailwind Typography (for `.prose`) is already installed above.  
Add base HTML resets for Markdown to **`styles/globals.css`**:

```css
@layer base {
  .markdown-content h1 { font-size: 2em; font-weight: bold; margin: 0.67em 0; }
  .markdown-content h2 { font-size: 1.5em; font-weight: bold; margin: 0.83em 0; }
  .markdown-content h3 { font-size: 1.17em; font-weight: bold; margin: 1em 0; }
  .markdown-content h4 { font-size: 1em; font-weight: bold; margin: 1.33em 0; }
  .markdown-content h5 { font-size: 0.83em; font-weight: bold; margin: 1.67em 0; }
  .markdown-content h6 { font-size: 0.67em; font-weight: bold; margin: 2.33em 0; }
  .markdown-content p { margin: 1em 0; }
  .markdown-content ul { list-style-type: disc; padding-left: 2em; margin: 1em 0; }
  .markdown-content ol { list-style-type: decimal; padding-left: 2em; margin: 1em 0; }
  .markdown-content li { margin: 0.25em 0; }
  .markdown-content strong { font-weight: bold; }
  .markdown-content em { font-style: italic; }
  .markdown-content hr { border: 0; border-top: 1px solid #e5e7eb; margin: 2em 0; }
}
```

---

## ☁️ Deployment (Vercel)

> You don’t need a `vercel.json`. Vercel auto-detects Next.js and Python in `/api`.

1) **Link** the local directory to a Vercel project:

```bash
vercel link
```

2) **Add** your environment variable:

```bash
vercel env add OPENAI_API_KEY
```

3) **Preview Deploy**:

```bash
vercel .
```

4) **Production Deploy**:

```bash
vercel --prod
```

---

## 🧪 NPM Scripts

Add or use:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  }
}
```

Local dev note: The FastAPI function runs on Vercel’s serverless runtime. For end-to-end validation, use **deployed** preview/production URLs as shown above.

---

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
