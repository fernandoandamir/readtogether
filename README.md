# ReadTogether — Vercel Deploy

## Deploy in 2 minutes

### Option A — Vercel CLI (fastest)
```bash
npm i -g vercel
cd readtogether
vercel
```
Follow the prompts. Done.

### Option B — GitHub + Vercel dashboard
1. Push this folder to a GitHub repo
2. Go to https://vercel.com/new
3. Import the repo
4. Click Deploy — no settings needed

---

## Project structure
```
readtogether/
├── pages/
│   ├── index.jsx          # App entry point
│   └── api/
│       └── proxy.js       # Server-side CORS proxy (runs on Vercel)
├── components/
│   └── BookReader.jsx     # Full reader UI
├── next.config.js
├── vercel.json            # Sets proxy timeout to 45s
└── package.json
```

## How the proxy works

`pages/api/proxy.js` is a Next.js API route that runs server-side on Vercel.
When the reader calls `/api/proxy?url=https://...`, Vercel fetches the URL
from its own servers (no CORS restrictions), using:

- **4 rotating browser fingerprints** (Chrome/Win, Chrome/Mac, Firefox, Safari)
- **Full Sec-CH-UA / Sec-Fetch-* headers** to avoid bot detection
- **Per-request cookie jar** with manual redirect following
- **Smart URL transforms**: Google Docs → export?format=txt, Pastebin → /raw/, GitHub → raw.githubusercontent.com
- **Header stripping**: removes X-Frame-Options, CSP, HSTS etc. before forwarding

If `/api/proxy` fails, the reader falls back to two public CORS proxies.

## Limits
- Vercel Hobby: 45s function timeout, 100GB bandwidth/month — plenty for a reader
- Sites behind Cloudflare JS challenges or login walls still won't load (fundamental limit)
- Google Docs must have sharing set to "Anyone with the link can view"
