# Sir Kanha Worm GPT — Deployment Guide

## What you need before deploying

1. **PostgreSQL database** (free options):
   - https://neon.tech — free tier, copy the connection string
   - https://supabase.com — free tier

2. **Clerk account** (free):
   - Go to https://clerk.com → Create a new application
   - Enable Google as a sign-in provider
   - Copy your API keys from the "API Keys" page

---

## Option A — Deploy on Vercel (recommended)

### 1. Push to GitHub
Create a GitHub repo and upload all files to it.

### 2. Connect to Vercel
- Go to https://vercel.com → New Project → Import your GitHub repo

### 3. Build settings in Vercel
- **Framework Preset**: Other
- **Root Directory**: leave blank (monorepo root)
- **Install Command**: `npm install -g pnpm && pnpm install`
- **Build Command**: `BASE_PATH=/ pnpm --filter @workspace/worm-gpt run build`
- **Output Directory**: `artifacts/worm-gpt/dist`

### 4. Environment variables (Vercel → Settings → Env Vars)

| Variable | Value |
|---|---|
| `DATABASE_URL` | Your PostgreSQL connection string |
| `CLERK_SECRET_KEY` | From Clerk dashboard → API Keys |
| `CLERK_PUBLISHABLE_KEY` | Same value as above |
| `VITE_CLERK_PUBLISHABLE_KEY` | Same value as above |

### 5. For the API (backend)
The API (Express server) needs to be deployed separately on a service that supports Node.js, such as:
- **Railway** — https://railway.app (easiest, free tier)
- **Render** — https://render.com (free tier)
- **Fly.io** — https://fly.io

---

## Option B — Deploy everything on Railway (easiest)

Railway handles full-stack monorepos including PostgreSQL:

1. Go to https://railway.app → New Project → Deploy from GitHub
2. Add a PostgreSQL plugin (auto-fills DATABASE_URL)
3. Set environment variables (same as above)
4. Set **start command**: `pnpm --filter @workspace/api-server run start`
5. Serve frontend build output with a static site plugin

---

## Local development

```bash
# 1. Install pnpm
npm install -g pnpm

# 2. Install dependencies
pnpm install

# 3. Copy and fill environment variables
cp .env.example .env
# Edit .env with your values

# 4. Push database schema
pnpm --filter @workspace/db run push

# 5. Start API server (terminal 1)
PORT=5000 pnpm --filter @workspace/api-server run dev

# 6. Start frontend (terminal 2)
PORT=3001 BASE_PATH=/ pnpm --filter @workspace/worm-gpt run dev
```

Open: http://localhost:3001

---

## Project structure

```
sir-kanha-worm-gpt/
├── artifacts/
│   ├── worm-gpt/          React + Vite frontend
│   └── api-server/        Express API backend
├── lib/
│   ├── db/                Database schema (Drizzle ORM + PostgreSQL)
│   ├── api-spec/          OpenAPI specification
│   ├── api-client-react/  Generated React Query hooks
│   └── api-zod/           Generated Zod validation schemas
├── vercel.json            Vercel config
├── .env.example           Environment variables template
└── DEPLOY.md              This file
```

---

## Features

- Google login via Clerk
- 2 free AI messages per day per user
- 40-second cooldown between messages
- Full conversation history
- Credit expiry countdown screen
- Deep crimson glassmorphism design
