# beck.visionion.dev — marketing site

This folder is the static marketing website for **Beck**, the iOS task manager by
Visionion (bundle `dev.visionion.beck`). It lives at **https://beck.visionion.dev**.

These pages are required for App Store submission — the App Store listing and the
in-app paywall link to `/privacy`, `/terms`, and `/support`.

> **Status: drafts.** The legal pages (Privacy, Terms) are honest, plain-language
> drafts written to match how the app actually behaves. Review them (ideally with
> your own counsel) before publishing.

## Contents

```
beck-site/
├── index.html            Landing page
├── privacy/index.html    Privacy Policy   → /privacy
├── terms/index.html      Terms of Service → /terms
├── support/index.html    Support / FAQ    → /support
├── styles.css            Shared stylesheet (linked as /styles.css)
├── robots.txt
└── README.md             This file
```

Plain HTML + CSS. No frameworks, no build step, no external JS beyond the Google
Analytics snippet. Light/dark themes come from the OS via `prefers-color-scheme`.

## Clean URLs

Each section is a folder with an `index.html`, so any static host serves clean
URLs automatically:

- `/privacy/index.html`  → **/privacy**
- `/terms/index.html`    → **/terms**
- `/support/index.html`  → **/support**

Netlify, Vercel, Cloudflare Pages, and GitHub Pages all do this out of the box —
no rewrite rules needed. The stylesheet is linked with a root-absolute path
(`/styles.css`) so it resolves correctly from every page.

## Before you publish — fill these in

1. **App Store link.** On the landing page (`index.html`), the hero
   "Download on the App Store" button uses `href="#"`. Replace it with the real
   App Store product URL once the app is live. (Search `href="#"` in `index.html`.)

2. **Google Analytics Measurement ID.** Every page has a GA4 gtag.js snippet in
   `<head>` using the placeholder **`G-XXXXXXXXXX`** (this is the Visionion GA
   setup, property **516785066**). GA needs the *Measurement ID* (the `G-…`
   string from the GA Admin → Data Streams → Web stream), not the numeric
   property id. Replace `G-XXXXXXXXXX` in all files:

   ```
   grep -rl "G-XXXXXXXXXX" .
   ```

   It appears twice per page (in the `<script src>` and in `gtag('config', …)`) —
   replace both, in `index.html`, `privacy/index.html`, `terms/index.html`, and
   `support/index.html`. GA is disclosed on the site (it runs on this marketing
   site only, never in the app).

## Deploy

This is a plain static folder — deploy it however you like:

- **Drag & drop:** zip or drag the `beck-site/` folder into Netlify
  (app.netlify.com → "Add new site" → "Deploy manually") or Cloudflare Pages.
- **Git-connected:** push this folder to a repo and connect it in Netlify /
  Vercel / Cloudflare Pages. No build command; publish directory is the folder
  root. (This folder intentionally has no git repo yet — set that up separately
  if you want CI deploys.)
- **GitHub Pages:** push to a repo and enable Pages on the root.

## DNS — point beck.visionion.dev at the host

Visionion DNS for **visionion.dev** is on **Porkbun**. After deploying, add a
record for the `beck` subdomain pointing at your host:

- Typically a **CNAME** `beck` → the host's target
  (e.g. `your-site.netlify.app`, `cname.vercel-dns.com`, or the Pages hostname),
  or the A/AAAA records the host gives you.
- Then add `beck.visionion.dev` as a custom domain in the host's dashboard and
  let it provision the TLS certificate.

You can manage the Porkbun record with the `porkbun-dns` skill / `porkbun.sh`
helper (Porkbun requires per-domain API access to be toggled on first).

## Notes on accuracy

The copy is deliberately conservative and matches the app's real behavior:
local-first storage (GRDB/SQLite, **not** claimed as encrypted-at-rest),
optional Supabase-backed sync with email+password auth, tokens in the iOS
Keychain, HTTPS in transit, on-device-only location, and no third-party
analytics/ads/data-selling in the app. Don't add claims the app can't back up.
