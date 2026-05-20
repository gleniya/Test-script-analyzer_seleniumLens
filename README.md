# SeleniumLens — AI-Powered Selenium Script Analyzer

A production-ready tool for QA engineers to analyze Selenium test scripts for issues in locators, timing, naming, structure, and best practices.

## Features

- 🎯 **Fragile locator detection** — Identifies brittle XPaths and suggests stable alternatives
- ⏱ **Timing issues** — Flags Thread.sleep and missing explicit waits
- ✏️ **Naming violations** — Catches poor method/variable names
- 🏗 **Structural analysis** — Recommends Page Object Model patterns
- ✓ **Assertion gaps** — Detects missing or weak assertions  
- ⭐ **Best practices** — General Selenium anti-pattern detection
- 📊 **Quality score** — A–F grading with 0–100 score

## Setup

### 1. Install dependencies
```bash
npm install
```

### 2. Set up your Anthropic API key

Create a `.env.local` file:
```
VITE_ANTHROPIC_API_KEY=sk-ant-your-key-here
```

Then update `src/App.jsx` — find the fetch call and add the header:
```js
headers: {
  'Content-Type': 'application/json',
  'x-api-key': import.meta.env.VITE_ANTHROPIC_API_KEY,
  'anthropic-version': '2023-06-01',
  'anthropic-dangerous-direct-browser-access': 'true',
},
```

### 3. Run locally
```bash
npm run dev
```

## Deploy to Vercel

### Option A: Via Vercel CLI
```bash
npm install -g vercel
vercel
```

When prompted, add environment variable:
- `ANTHROPIC_API_KEY` = your Anthropic API key

### Option B: Via Vercel Dashboard
1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → New Project → Import repo
3. Add environment variable: `ANTHROPIC_API_KEY`
4. Deploy

### Production API key handling (recommended)
For production, use a Vercel serverless function instead of calling the Anthropic API directly from the browser. Create `api/analyze.js`:

```js
export default async function handler(req, res) {
  if (req.method !== 'POST') return res.status(405).end()
  
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.ANTHROPIC_API_KEY,
      'anthropic-version': '2023-06-01',
    },
    body: JSON.stringify(req.body)
  })
  
  const data = await response.json()
  res.json(data)
}
```

Then change the fetch URL in `App.jsx` from `https://api.anthropic.com/v1/messages` to `/api/analyze`.

## Tech Stack
- React 18 + Vite
- Claude claude-sonnet-4-20250514 (Anthropic API)
- Pure CSS (no UI library)
- Vercel for deployment
