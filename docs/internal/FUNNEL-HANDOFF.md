# Production Funnel Prototype Handoff — Stratemark

**Branch:** `feat/production-try-download-funnel`

**Purpose:** show the intended production acquisition flow without building the hosted app, billing, entitlement enforcement, or desktop packaging in this landing-page repository. The existing brand, typography, spacing, motion, and component language are preserved. The page is a reviewable prototype for engineering and design.

Last updated: 2026-08-27.

## Approved visitor flow

### Top-level actions

1. **Try Now**
   - Opens the free-trial explanation before the visitor leaves the landing page.
   - Promises one complete market search, not a pre-built example and not an arbitrary number of Cloud Run calls.
   - The visitor chooses any market of interest, big or small.
   - Google sign-in binds the trial to the account.
   - After one complete deck is built, the visitor can explore it before the paywall.

2. **Download**
   - Scrolls to the three product paths on the landing page.
   - The highlighted path is the easy desktop installer.

### Three product paths

| Path | Price | Who pays for AI usage | Delivery |
| --- | ---: | --- | --- |
| Open source | $0 | User, with their Gemini key | Canonical GitHub repository after account migration |
| Easy install | $1–$100 once, default $29 | User, with their Gemini key | Signed Windows/macOS desktop installer plus hosted access |
| Managed subscription | Starter $19 / Pro $49 / Max $99 monthly | Stratemark | Hosted web app plus desktop access |

Managed monthly allowances in this prototype follow `docs/PRICING-MODEL.md` from the product repo at commit `3956419`:

- Starter: 12 decks
- Pro: 35 decks
- Max: 70 decks
- Planned top-up: 10 decks for $12

Do not substitute the older app values of 10 / 40 / 150 or rename Pro to Growth without Shannon's approval.

## Free-trial contract

The static landing page can explain the trial but cannot enforce it. The hosted product must enforce the following before this branch is production-ready:

1. Authenticate with Google.
2. Look up the user's trial entitlement server-side.
3. Allow exactly one complete market-search job per eligible account.
4. Reserve the entitlement when the job is accepted, not on every underlying model request.
5. Make retries for a failed job idempotent so infrastructure errors do not consume the visitor's only trial.
6. Record completion only after the deck reaches the usable completed state.
7. After completion, allow the visitor to explore that deck and show the conversion paywall before another new-market job.
8. Do not expose Cloud Run request counts as the customer-facing allowance. One deck currently spans many model requests.

Recommended post-trial message:

> Your free market search is complete. Keep researching your way: download Stratemark and bring your own Gemini key, or choose a managed plan and we’ll handle the API setup and usage for you.

The paywall must show the exact deck allowance for each managed tier before checkout.

## Required destination swaps

The prototype intentionally disables destinations that do not yet exist. Do not replace them with guesses.

| Landing control | Required destination | Owner / prerequisite |
| --- | --- | --- |
| Trial dialog → Continue to research | Permanent hosted web-app new-research URL | Maruf after Firebase Hosting and Cloud Run deploy |
| Open source → View on GitHub | Canonical imported repository URL | Shannon chooses Maruf or Tobi; engineering imports, not forks |
| Easy install → Download | Lemon Squeezy one-time checkout with chosen slider price | Tobi supplies checkout/store details; Maruf binds license entitlement |
| Subscription → View plans | Hosted plan-selection page or Lemon Squeezy subscription chooser | Tobi supplies Starter/Pro/Max variant IDs; Maruf binds entitlements |

Search `index.html` for `SWAP-POINT` to find each exact location.

The slider value is untrusted client input. Before enabling checkout, Tobi and Maruf must confirm the Lemon Squeezy pay-what-you-want mechanism, pass the selected amount through the supported checkout contract, and validate the $1–$100 range on the billing/entitlement side. If the checkout cannot accept a variable amount directly, use a Lemon Squeezy-hosted price chooser or explicit variants rather than inventing a URL parameter.

## GitHub rule

The live landing page currently points to stale `squarepegng/stratemark`, while the product app and submission document point to `NewSamBellamy/STRATEMARK`. Neither is the approved long-term destination.

- Pick Maruf's or Tobi's account.
- Import the repository rather than forking so the full hackathon commit history is preserved.
- Update every product-repo reference and the landing page in the same coordinated cutover.
- Until then, this prototype shows a disabled GitHub control and a non-link footer label. It does not send visitors to the wrong repository.

## Engineering handoff

The landing page does not own the following work:

- GCP project or Cloud Run deployment
- Firebase Hosting, Auth, or Firestore
- one-trial-per-account enforcement
- Lemon Squeezy checkout or webhook processing
- per-tier quota enforcement
- desktop signing, installer packaging, or auto-update

Before wiring Lemon Squeezy, reconcile the product app's entitlement contract. At product commit `3956419`, `AuthContext.tsx` types `subscriptionTier` as only `free | pro`, while the commercial model needs `starter | pro | max` plus one-time-purchase and trial state. The UI also still shows the older $10 default and 10 / 40 / 150 deck allowances. That is product-repo work and must be resolved by the product/backend thread.

## Designer handoff

Review this branch as a flow prototype, not a redesign brief.

- Preserve the existing visual system.
- Review only the new dialog, the two CTA labels, and the three-card pricing composition.
- Keep the visitor promise explicit: a real search on the visitor's chosen market, not a gallery of pre-built decks.
- Keep the free trial framed as one complete market search.
- Do not reintroduce separate example or sample-deck CTAs.
- The grey buttons are deliberately disabled handoff states, not final styling decisions.

## Production gate

Do not merge this prototype to the production landing branch until all four destination swaps are real, the product entitlement model is reconciled, the trial retry policy is implemented, and engineering plus design have approved the final flow.
