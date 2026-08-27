# Deploying the Stratemark landing page

## Current production path: Netlify

The live site (`getstratemark.com`) deploys from this repository via Netlify
(project `stratemark`), branch `main`. Push to `main` → Netlify builds → live.
There is no build step: the repo is static HTML. Netlify post-processing is ON
(pretty URLs: `href="demo.html"` is served as `/demo`).

Since 2026-08-27, `netlify.toml` in this repo controls the deploy: security
headers + CSP, asset caching, and redirects (see that file — **do not edit
the CSP hashes unless you changed the inline script/style in index.html or
demo.html**; the recompute one-liner is in its header comment).

## Alternative path: Firebase Hosting (Google Cloud)

If/when the site needs to live under the hackathon's Google Cloud project
(the Firebase Hosting option Maruf's handover describes), one-time setup on
any machine with Node:
1. `npm install -g firebase-tools`
2. `firebase login`  (use the Square Peg / project Google account)
3. In the Firebase console, create a project (e.g. `stratemark-landing`).
   Using a project under the hackathon's Google Cloud credits satisfies the
   "must use at least one Google Cloud product" rule with zero ambiguity.

Deploy (from this folder):
1. `firebase use --add`   → pick the project, alias it `default`
2. `firebase deploy --only hosting`

`firebase.json` here is minimal: it publishes the whole folder with cache
headers for images only. If Firebase Hosting becomes the production host,
port the security headers and CSP from `netlify.toml` into `firebase.json`
`hosting.headers` (same values, same hashes), and re-check the pretty-URL
assumption — the landing page links use `demo.html`-style hrefs.

BEFORE any deploy, check the swap list in `docs/internal/SWAP-POINTS.md`.
