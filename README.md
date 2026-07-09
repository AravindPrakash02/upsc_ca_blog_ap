# Aspirants' Parliament

A personal UPSC current affairs platform — daily articles, MCQ practice sets, curated quizzes, and PDF resources, structured for serious civil services aspirants.

Built as a zero-dependency, two-file static site running entirely on Cloudflare's edge infrastructure.

**Live site:** https://cf-workerjs.meetcheetah.workers.dev/
---

## Architecture

```
Browser
  └── Cloudflare Worker (GET /)
        └── Serves index.html from KV
              └── Loads content: Worker KV → GitHub backup → localStorage

Admin Panel (admin.html)
  └── Publishes to Cloudflare Worker KV
        ├── backup.json  (content + branding)
        └── index.html   (public site)
```

**No build step. No framework. No server.** Two HTML files — one public site, one admin panel.

---

## Stack

| Layer | Choice |
|---|---|
| Hosting | Cloudflare Workers (public site) + Cloudflare Pages (admin) |
| Storage | Cloudflare KV |
| Fonts | Cormorant Garamond · Inter · Libre Baskerville (Google Fonts) |
| Icons | Tabler Icons |
| Theming | CSS custom properties, light/dark/auto with OS-preference detection |

No npm. No bundler. No runtime dependencies.

---

## Features

**Public site**
- Bento-grid layout with subject-taxonomy colour coding (Polity, Economy, Science, History, Geography, IR, Environment, Society)
- Daily current affairs articles with full-text modal reader
- Archive view — browse articles by date
- MCQ practice sets with timed test mode and scoring
- Daily quizzes with past-quiz history
- PDF resource library
- Search across all content
- Light / Dark / Auto theme toggle (flash-free bootstrap)
- Branding-driven — site name, logo, hero image, social links, contact details all configurable from admin without touching code

**Admin panel**
- Login-gated (session persisted in localStorage)
- Three-tier data load on login: Worker KV (live) → GitHub backup (fallback) → browser localStorage (last resort)
- Data-source badge shows which tier is active
- Publish content + branding to KV in one click
- AI-assisted article formatting (Gemini API) — extracts key points, tags subjects, structures output
- Export / import backup.json for local snapshots
- File-upload guard: detects and rejects accidentally uploading admin.html as the public index

---

## Data Flow

1. Admin writes content → clicks Publish → `backup.json` + `index.html` written to KV via Worker
2. Public site loads → Worker serves `index.html` from KV
3. `index.html` calls `loadLiveContent()` → tries Worker KV first, GitHub backup second, localStorage third
4. Branding applied at runtime via `applyRuntimeBranding()` — CSS variables + DOM targets updated without re-render

---

## File Structure

```
/
├── index.html      # Public site (served by Worker from KV)
└── admin.html      # Admin panel (deployed to Cloudflare Pages)
```

Content and branding live in Cloudflare KV, not in the repo — the HTML files are the application shell.

---

## Local Development

No build step required. Open either file directly in a browser.

For admin panel data loading to work locally, set the Worker URL and GitHub settings in the Settings tab — the data-source fallback chain will resolve correctly across environments.

---

## Deployment

1. Deploy the Worker script (handles `GET /`, `/publish`, `/backup`) to Cloudflare Workers
2. Create a KV namespace and bind it to the Worker
3. Deploy `admin.html` to Cloudflare Pages
4. Open the admin panel → Settings → set Worker URL + GitHub repo details
5. Publish once to seed KV with initial content

---

## Status

Track 1 — personal platform, feature-complete, in maintenance mode.

A white-label version of this platform has been delivered to a coaching institute client as a separate, independent deployment. Track 2 — a proper multi-tenant SaaS built on Cloudflare D1 with role-aware admin, billing integration, and structural layout presets per tenant — is planned as a future project.
