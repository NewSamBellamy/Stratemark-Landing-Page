# Backend & GCP Wiring Handoff — Stratemark

**Audience:** backend developer (Maruf). Everything in this doc is what remains
between the landing page shipped today and a wired-up Google Cloud backend.
The landing-page side is done and hardened as far as it can go without a GCP
project. Last updated: 2026-08-27.

## State right now

- **Live:** `getstratemark.com` on Netlify, project `stratemark`, deploys from
  `NewSamBellamy/Stratemark-Landing-Page` branch `main`. Latest deploy commit
  before this cleanup: `f1f9c1d`.
- **Architecture today:** BYOK only. `demo.html` is the single-file build of
  the web app (built with `SINGLEFILE=1`). With no Gemini key it runs on a
  built-in sample snapshot; with a key it calls `generativelanguage.googleapis.com`
  directly from the browser. There is **no server** behind the site yet — no
  Firebase, no Cloud Run, no API key anywhere in the repo (scanned: zero
  `AIza…`/tokens/`.env`).
- **Auth:** none on the landing/demo yet. The app code in the monorepo
  (`STRATEMARK-latest-`, formerly `STRATEMARK`) feature-detects Firebase config
  — the moment the four env vars exist at build time, the Google sign-in UI
  lights up. Nothing to change in the UI.
- **Sentinel:** the single-file preview build ships with **no** sentinel API
  reference. The non-singlefile build embeds
  `https://stratemark-sentinel-api.a.run.app` as a constant in
  `lib/sentinelApi.ts` — that URL must become yours when you deploy the Cloud
  Run service.

## What's already done for you (landing-page side)

1. **`netlify.toml` added** — security headers on every response:
   - **CSP** with SHA-256 hashes for the two inline scripts and two inline
     style blocks. No `unsafe-inline`, no `unsafe-eval`.
   - `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`,
     `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy`
     (camera/mic/geo/payment/usb off), COOP + CORP `same-origin`,
     `upgrade-insecure-requests`.
   - **`connect-src` is pre-opened** for the endpoints you will need:
     `https://identitytoolkit.googleapis.com`, `https://securetoken.googleapis.com`
     (Firebase Auth), `https://firestore.googleapis.com`,
     `https://firebase.googleapis.com`, and `https://*.run.app` (Sentinel).
     You should not have to touch CSP when wiring Firebase — unless the web
     app gains other fetch targets, or you deploy the Sentinel under a
     non-`run.app` host (then narrow the wildcard).
   - Redirects: `index-enhanced.html` → `/` (orphan duplicate, retired),
     `/index.html` → `/`, and **403 blocks** for `/DEPLOY.md`,
     `/SWAP-POINTS.md`, `/firebase.json`, `/.21st/*`, `/docs/*` — these were
     publicly served before.
2. **`robots.txt` + `sitemap.xml` + `404.html`** added.
3. **og:image** share card (1200×630, brand-consistent) designed and handed
   off as the `og-share.png` file — it needs a normal binary git push into
   `img/` (the agent's text-only GitHub API corrupts binary bytes; a test
   push was verified corrupt and removed). `docs/internal/SWAP-POINTS.md`
   item 5 has the exact meta lines to add to `index.html` once the file is
   in place — they don't touch the inline blocks, so CSP hashes stay valid.
4. Internal docs moved to `docs/internal/` and blocked from serving (see 1).

## Your wiring checklist (in order, from Maruf's CTO handover)

1. **Google Cloud project + billing guardrails.** Create the project; set Cloud
   Run `max-instances` (start at 2) and billing alerts on day one.
   Scale-to-zero default.
2. **Firebase Auth (Google sign-in).** Enable the provider, authorize
   `getstratemark.com` (+ whatever hosts the web app build), and supply the four
   build-time vars for the web build:
   `VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_AUTH_DOMAIN`,
   `VITE_FIREBASE_PROJECT_ID`, `VITE_FIREBASE_APP_ID`. Then retire the preview
   access-code gate in the monorepo (`lib/auth/RequireAuth.tsx`,
   `lib/access.ts`, their tests, `e2e/access.ts`).
3. **Firebase Hosting for the web app.** Plain Vite build — **not**
   `SINGLEFILE=1` (that flag is for single-HTML preview artifacts only).
   `pnpm --filter @mi/web build` → deploy `apps/web/dist`. Hash routing ⇒ no
   SPA rewrites strictly required.
4. **Cloud Run — the Sentinel agent.** Hackathon-critical. The client constant
   `https://stratemark-sentinel-api.a.run.app` in `lib/sentinelApi.ts` is the
   contract; deploy under that name or update the constant + rebuild. This is
   also the honest place to satisfy the "at least one Google agent framework"
   requirement — `packages/research` is isomorphic, so the same pipeline runs
   server-side on a Cloud Scheduler. CSP already allows `*.run.app`.
5. **Firestore sync (Pro).** Last-write-wins on the whole `RepoSnapshot` with
   `updatedAt` (matches the IndexedDB vault mirror behavior). Rules: a user
   reads/writes only `users/{uid}/**`. **Never sync the user's Gemini key.**
   (`firestore.rules` in the monorepo currently has a recursive-wildcard
   `match /{document=**}` — rewrite it to enumerate collections when you
   deploy; the current rule grants client write to every present and future
   subcollection.)
6. **Lemon Squeezy entitlements.** Store id + variant ids come from Tobi.
   Webhook → small Cloud Run endpoint → match customer email to Firebase user →
   write `subscriptionTier` to `/users/{uid}`. The client already reads
   `user.subscriptionTier`.
7. **License gating** for the one-time "Easy install" purchase (installer +
   hosted web app unlock). Implementation is free-form; nothing in the client
   presumes it.

## Decisions you're inheriting (don't re-litigate without Shannon)

- The landing pricing intentionally shows **Free + Easy install only** and says
  "Nothing here is a subscription." The in-app pricing shows all three doors
  (Free / subscriptions $19/$49/$99 / easy install). Tobi's handover flagged
  that the site should eventually match the app — that is a **copy decision
  for Shannon**, not a backend task.
- BYOK engine stays client-side forever. The Gemini key is never server-side,
  never in env.
- Max tier economics ($99 for 150 decks) is margin-negative at Gemini list
  prices — fair-use cap vs reprice is Shannon + Tobi's call.

## Known watch-outs

- **CSP hash fragility:** any edit to the inline script/style in `index.html`
  or `demo.html` breaks the page with a CSP violation until the hash in
  `netlify.toml` is recomputed (one-liner in the file's header comment).
  Rebuilding `demo.html` from the monorepo **will** change both hashes.
- **Bundle size:** main chunk ~1.26 MB (375 KB gzip) in the monorepo build;
  route-level code splitting is the fix if it matters.
- **`squarepegng/stratemark`** (public GitHub repo the landing links to,
  incl. the pricing "Get easy install" button → `/releases`) has **no releases
  yet**. That CTA currently lands on an empty releases page. Tobi's store ids
  + your license gating close this loop; until then it is a soft dead end.
- **Legal pages are launch drafts**: yellow placeholder chips on
  privacy/terms/refunds/contact await Square Peg NG's exact legal name,
  support email/phone, and registered address. Content task, tracked in
  `docs/internal/SWAP-POINTS.md`.
