# NDF-BD-ctg-platform
Full-stack community platform for a debate org — React, Supabase, Hono on Cloudflare Workers, PWA. 3-session security audit. 30+ pages, 9 DB tables.
# NDF BD Chattogram — Community Platform

**National Debate Federation Bangladesh, Chittagong Chapter**
A full-stack community platform for a local debate organization — built from scratch as a freelance portfolio project over 15 development sessions.

🌐 **Live:** [ndfbdctg.org](https://ndfbdctg.org) &nbsp;|&nbsp; 📱 **PWA installable on Android & iOS**

---

## What this is

A production-ready community web app for a debate organization — covering public-facing content, member management, event records, a blog, a Foundation Debate Course (FDC) hall of records, and a full admin panel. Built and hardened for real-world use.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite (JavaScript) |
| Styling | Tailwind CSS + Shadcn/UI (Nova preset) |
| Routing | React Router v6 |
| Backend / API | Hono on Cloudflare Workers |
| Database | Supabase (Postgres + RLS + Auth) |
| Storage | Supabase Storage (3 buckets) |
| Deployment | Vercel (frontend) + Cloudflare Workers (API) |
| PWA | vite-plugin-pwa + custom service worker |

---

## Architecture

```
Browser (React PWA)
    ↓
Cloudflare Workers — Hono API layer
    ↓  (all table reads/writes proxied here)
Supabase Postgres (RLS enforced at DB level)
    ↓
Supabase Storage (avatars, event-photos, post-covers)
```

Auth and storage calls go direct to Supabase SDK. All data table calls go through Hono — the anon key never touches the frontend.

---

## Features

### Public
- Home page with animated announcement ticker, stats row, upcoming events, latest blog posts
- Events listing with search and filters
- Event detail with photo gallery + lightbox
- Blog with post detail pages
- Announcements and notices board
- Photo gallery
- FDC Hall of Records — year-wise summary + searchable participant table
- About and Contact pages

### Member (authenticated)
- Role-gated access (pending → admin approval → member)
- Member dashboard with avatar, display name, bio
- Write and publish blog posts with cover image upload
- Edit profile (display name, bio, avatar)
- My Posts management
- Password change

### Admin
- Admin dashboard with live stats
- Manage Members — approve/reject pending signups, add members
- Manage Events — full CRUD
- Manage Announcements — full CRUD with pin toggle
- Manage Notices — full CRUD
- Manage Blog Posts — approve/reject/delete member posts
- Manage FDC Records — add/edit/delete course participant records

### Infrastructure
- Hono proxy layer hiding Supabase anon key
- JWT verification middleware on every API route
- Rate limiting per IP (60 req/min, upgradeable to Cloudflare KV)
- Service worker with network-first caching strategy
- PWA manifest with full icon set (72px → 512px)
- Supabase keepalive cron (prevents free tier pause)

---

## Database Schema

9 tables: `users`, `members`, `events`, `event_participants`, `photos`, `announcements`, `posts`, `notices`, `fdc_records`

RLS policies on all tables. Auth trigger auto-creates `users` row on signup. Role defaults to `pending` — admin approval required before member access is granted.

---

## Security

Hardened over 3 dedicated sessions:

- RLS enabled and verified on all 9 tables via direct Supabase API calls (PowerShell)
- Admin action spoofing tested with regular member JWTs — blocked
- Role self-escalation prevented via `WITH CHECK` on users UPDATE policy
- Supabase anon key hidden in Cloudflare Worker secrets (never in frontend)
- `service_role` key confirmed absent from all frontend and env files
- Security headers via `vercel.json` (OWASP recommended set)
- Brute force protection enabled in Supabase Auth
- Public membership signup removed — admin-only member creation
- Input sanitization in Hono before DB writes

**Security rating: 8/10 current → 9.5/10 target after Cloudflare DNS + live domain**

---

## Design System

Locked design tokens used consistently across all 30+ pages:

- **Background:** Warm breathing diagonal gradient animation (14s cycle)
- **Cards:** `#FFFDF9`, amber shadow, 1px white border
- **Display font:** Fraunces 800
- **Body font:** DM Sans
- **Primary green:** `#006A4E` | **Red accent:** `#D42027`
- Mobile-first throughout. Fixed bottom navigation.

---

## Project Stats

| | |
|---|---|
| Development sessions | 15 |
| Pages built | 30+ |
| Database tables | 9 |
| Storage buckets | 3 |
| API routes (Hono) | 7 modules |
| Security audit sessions | 3 |

---

## Local Development

```bash
# Frontend
npm install
cp .env.example .env.local   # fill in Supabase credentials
npm run dev

# API (Cloudflare Workers / Hono)
cd ndfapi
npm install
cp .env.example .dev.vars    # fill in secrets
npx wrangler dev             # runs on localhost:8787
```

---

## Deployment

```bash
# Deploy API first
cd ndfapi
npm run deploy
wrangler secret put SUPABASE_ANON_KEY
wrangler secret put SUPABASE_JWT_SECRET

# Deploy frontend
npm run build
# Push to GitHub → Vercel auto-deploys
```

---

## Status

- [x] Full build complete
- [x] PWA configured and tested
- [x] Security hardening complete (RLS, Hono, headers)
- [x] Deployed to Vercel
- [ ] Custom domain (ndfbdctg.org) — pending purchase
- [ ] Cloudflare DNS in front of Vercel
- [ ] AI chatbot feature (Groq API, scoped to org knowledge)

---

*Built by [Rakesh Das](https://github.com/Rakesh-84) — freelance React developer, Chittagong, Bangladesh.*
