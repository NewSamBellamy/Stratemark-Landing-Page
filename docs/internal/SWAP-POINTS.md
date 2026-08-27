# Swap points before production deploy

Internal checklist. This file is blocked from public serving via `netlify.toml`
redirects (serves 403). Search the site for `SWAP-POINT` comments and yellow
placeholder chips on legal pages.

## Required swaps

1. **GitHub repository URL — DONE (12 Aug)**
   All links point at `https://github.com/squarepegng/stratemark` (nav, hero,
   pricing, footer). Note: that repo currently has **no releases**, so the
   "Get easy install" button (→ `/releases`) lands on an empty page until
   Tobi's Lemon Squeezy checkout or a first GitHub release exists. See item 3.

2. **Demo URL — DONE (16 Aug, `f1f9c1d`)**
   All CTAs point at native `demo.html` in this repo (served as `/demo`).

3. **Lemon Squeezy checkout URL — OPEN**
   Easy-install primary button currently points at the GitHub releases
   placeholder. Swap to the Lemon Squeezy checkout URL / overlay once the
   store is approved (Tobi owns the store; Maruf wires the entitlement
   webhook — see `BACKEND-HANDOFF.md`).

4. **Square Peg NG legal details — OPEN**
   Yellow chips (`class="ph"`) on privacy / terms / refunds / contact pages:
   - Exact legal name (match Lemon Squeezy + CAC)
   - Support email
   - Support phone
   - Registered address in Nigeria
   - RC / tax IDs if any
   The visible "Awaiting Tobi / Square Peg NG" banners on those pages are
   intentional draft notices — remove them together with the last chip.

5. **og:image — ASSET READY, AWAITING BINARY PUSH (27 Aug)**
   The 1200×630 share card has been designed (brand-consistent: ink
   background, dot-grid, teal mark motif, headline + subline + wordmark) and
   is ready as `og-share.png`. It could not be committed via the agent's
   text-only GitHub API (binary bytes get re-encoded and corrupted — a test
   push was verified corrupt and removed again in `73ac284`).
   **To finish:** drop the delivered `og-share.png` into `img/` (GitHub web
   UI upload or `git add img/og-share.png`), then add to `index.html`
   `<head>` right after the `og:type` line:
   ```html
   <meta property="og:url" content="https://getstratemark.com/" />
   <meta property="og:image" content="https://getstratemark.com/img/og-share.png" />
   <meta property="og:image:width" content="1200" />
   <meta property="og:image:height" content="630" />
   <meta name="twitter:card" content="summary_large_image" />
   <meta name="twitter:image" content="https://getstratemark.com/img/og-share.png" />
   ```
   These lines do not touch the inline script/style blocks, so the CSP
   hashes in `netlify.toml` stay valid.

## Do not

- Deploy to the production custom domain until CTO signs off on this list.
- Edit the inline script/style in `index.html` or `demo.html` without
  recomputing the CSP hashes in `netlify.toml` (one-liner in its header).

## Resolved in the 12 Aug design pass

- Footer and legal cross-links point at local `privacy.html` / `terms.html` /
  `refunds.html` / `contact.html` (previously external Hyperagent artifact
  URLs, which would not have satisfied Paddle's "legal pages on your domain"
  requirement).
- Favicon wired to the brand mark.

## Resolved in the 27 Aug security cleanup

- `DEPLOY.md`, `SWAP-POINTS.md`, `.21st/` no longer served publicly
  (moved to `docs/internal/` + 403 redirects). `firebase.json` stays at the
  repo root (needed by `firebase deploy`) but is 403-blocked from serving.
- `netlify.toml` added: CSP with inline hashes, security headers,
  `index-enhanced.html` retired via redirect, robots.txt / sitemap.xml /
  404.html added.
- `BACKEND-HANDOFF.md` added at repo root: everything the backend developer
  needs to wire Google Cloud (Firebase Auth/Hosting, Cloud Run Sentinel,
  Firestore, Lemon Squeezy entitlements).
- og:image asset designed and delivered as a file handoff (item 5 above —
  needs a normal binary git push, then two lines of meta).
