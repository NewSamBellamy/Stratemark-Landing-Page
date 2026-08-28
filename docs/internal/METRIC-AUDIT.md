# Landing Metric Audit — Stratemark

Audit date: 2026-08-27

Scope: every user-visible quantitative company claim in `index.html` on branch `feat/production-try-download-funnel`. Pricing values are product decisions and are tracked separately in `FUNNEL-HANDOFF.md`.

## Rules applied

1. Public-company financials use the latest completed fiscal-year result from the issuer.
2. Private-company figures are labelled as annualized run rates, not annual revenue.
3. Funding valuations are labelled post-money and dated.
4. Dynamic market capitalizations were removed from the static report table because they change every trading day.
5. Private-company employee counts remain unknown unless the company publishes them.
6. Unsupported churn and cap-table percentages render as unknown rather than estimates.
7. Product scores are derived values, not externally observed metrics. The prototype uses the shipping CMS weights and rating formula, including signal coverage and the stored tier review nudge.
8. Every repeated figure must match the same value, unit, period, and confidence label across cards, dashboards, comparisons, chat examples, and reports.

## Published values

| Company | Metric | Landing value | Status | As-of / basis | Source |
| --- | --- | ---: | --- | --- | --- |
| OpenAI | Annualized revenue run rate | $40B+ | Reported | August 2026 | Bloomberg, Aug 13 2026 |
| OpenAI | Post-money valuation | $852B | Verified | March 31 2026 funding close | OpenAI |
| OpenAI | Weekly active users | 900M+ | Verified | March 31 2026 | OpenAI |
| OpenAI | Employees | Unknown | Not disclosed | No first-party public count | — |
| Anthropic | Annualized revenue run rate | $65B+ | Reported | End of July 2026 | Bloomberg, Aug 17 2026 |
| Anthropic | Post-money valuation | $965B | Verified | May 28 2026 Series H | Anthropic |
| Anthropic | Fortune 10 customers | 8 of 10 | Verified | February 12 2026 | Anthropic Series G announcement |
| Anthropic | Enterprise LLM spend share | 40% | Estimated | Menlo Ventures 2025 survey | Menlo Ventures |
| Anthropic | Employees | Unknown | Not disclosed | Public estimates conflict materially | — |
| Anthropic | Churn | Unknown | Not disclosed | No credible public figure | — |
| Anthropic | Cap table percentages | Unknown | Not disclosed | No credible exact public breakdown | — |
| NVIDIA | FY2026 revenue | $215.9B | Verified | Fiscal year ended Jan 25 2026 | NVIDIA investor relations |
| NVIDIA | FY2026 Data Center revenue | $193.7B | Verified | Fiscal year ended Jan 25 2026 | NVIDIA investor relations |
| NVIDIA | Employees | 42K | Verified | End of FY2026 | NVIDIA 10-K |
| NVIDIA | Data-center GPU share | about 86% | Estimated | Current analyst estimate; methodology varies | Secondary market coverage |
| Microsoft | FY2026 revenue | $331.8B | Verified | Fiscal year ended Jun 30 2026 | Microsoft investor relations |
| Microsoft | Employees | 223K | Verified | June 30 2026 | Microsoft 10-K |
| Mistral AI | Annualized revenue run rate | over $400M | Reported | February 2026 | Financial Times |
| Mistral AI | Post-money valuation | €11.7B | Verified | September 9 2025 Series C | Mistral AI |
| Mistral AI | Employees | Unknown | Not disclosed | No first-party public count | — |

## CMS prototype scores

| Company | Score used | Explanation |
| --- | ---: | --- |
| NVIDIA | 97 | Full signal coverage, Tier 8; updated verified fiscal metrics |
| Anthropic | 95 | Tier 8; three usable high-tier public signals, with missing users and team reducing coverage |
| OpenAI | 93 | Tier 8; strong public signals with private-company estimates reducing confidence |
| Mistral AI | 64 | Tier 5; smaller value, revenue, workforce, and enterprise-share signals |

The score is the product's composite maturity rating. It must not be described as an externally verified fact.

## Corrections made

- OpenAI run rate: $25B → $40B+
- OpenAI weekly users: 800M+ → 900M+
- Anthropic run rate: $47B → $65B+
- Anthropic employee estimate: about 5,000 → unknown
- Anthropic fabricated growth: +34% YoY → observed run-rate movement from $47B to $65B
- Anthropic fabricated churn: 1.8% → unknown
- Anthropic fabricated cap-table split: 42% / 31% / 17% → unknown
- NVIDIA revenue: rounded $216B → $215.9B FY2026
- NVIDIA market cap removed from the static cards/report; replaced with verified FY2026 Data Center revenue
- NVIDIA share: about 85% → about 86%, explicitly estimated
- Microsoft revenue: rounded $332B → $331.8B FY2026
- Microsoft employee count: 228K → 223K
- Microsoft market cap removed from the static report
- Mistral valuation: about $23B negotiation → confirmed €11.7B post-money valuation
- Mistral employee estimate: about 1,000 → unknown
- Relative-time labels such as “this week” and “2w ago” replaced with fixed dates
- Report metadata now states when figures were checked and links the supporting sources

## Sources

- OpenAI funding, users, valuation: https://openai.com/index/accelerating-the-next-phase-ai/
- OpenAI current run rate: https://www.bloomberg.com/news/articles/2026-08-13/openai-s-revenue-run-rate-tops-40-billion-ahead-of-ipo
- Anthropic Series G adoption: https://www.anthropic.com/news/anthropic-raises-30-billion-series-g-funding-380-billion-post-money-valuation
- Anthropic Series H valuation and May run rate: https://www.anthropic.com/news/series-h
- Anthropic July run rate: https://www.bloomberg.com/news/articles/2026-08-17/anthropic-revenue-run-rate-surpasses-65-billion-ahead-of-ipo
- NVIDIA FY2026: https://investor.nvidia.com/news/press-release-details/2026/NVIDIA-Announces-Financial-Results-for-Fourth-Quarter-and-Fiscal-2026/default.aspx
- Microsoft FY2026: https://www.microsoft.com/en-us/Investor/earnings/FY-2026-Q4/press-release-webcast
- Microsoft FY2026 10-K: https://www.sec.gov/Archives/edgar/data/789019/000119312526323660/msft-20260630.htm
- Mistral Series C: https://mistral.ai/news/mistral-ai-raises-1-7-b-to-accelerate-technological-progress-with-ai/
- Mistral run rate: https://www.ft.com/content/664249e7-e8d5-4425-b397-ad3ed590b305

## Maintenance gate

Any future copy change that touches a company number must update every occurrence in `index.html`, retain the period label, and add the source to this file. Market caps require a visible as-of date; otherwise omit them.
