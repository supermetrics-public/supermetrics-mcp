---
name: paid-media-optimization
description: Optimize paid media from live data: find wasted ad spend, search terms to negate, ad fatigue, creative winners, week-over-week changes, cross-platform and blended ROAS, promo anomalies, SKU and new-vs-returning ROAS, and budget reallocation across Google Ads, Meta, Microsoft, LinkedIn, TikTok and Amazon Ads. Use for any "where am I wasting money", "what changed", "which ads to pause" or ecommerce ROAS question.
---

# Paid media optimization

Workflows that turn live ad and ecommerce data into decisions a media buyer can act on today.

## Pick the workflow

| Question | Focus | Steps |
|---|---|---|
| Week-over-week campaign optimization | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 1 |
| Ad fatigue and frequency check | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 2 |
| Creative concept testing and iteration | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 3 |
| Daily morning standup check | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 4 |
| Search term audit and negative keywords | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 5 |
| Cross-channel attribution comparison | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 6 |
| Budget reallocation modeler | Performance | [references/performance-marketer-workflows.md](references/performance-marketer-workflows.md) — Workflow 7 |
| Cross-platform ROAS and purchase behavior | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 1 |
| Promotional campaign anomaly detection | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 2 |
| Product / SKU-level performance | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 3 |
| New vs returning customer ROAS by channel | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 4 |
| AOV and basket composition | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 5 |
| Subscription / retention cohort analysis | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 6 |
| Pre-promo planning model | Ecommerce | [references/ecommerce-marketer-workflows.md](references/ecommerce-marketer-workflows.md) — Workflow 7 |

Read the linked workflow before pulling data. Background for each focus area:

- Performance: principles, visualization and gotchas in [references/performance-marketer.md](references/performance-marketer.md)
- Ecommerce: principles, visualization and gotchas in [references/ecommerce-marketer.md](references/ecommerce-marketer.md)

## Before you analyze

- Load saved targets, budgets and known context with `manage_business_context` (`action: get`) and judge results against them, not generic benchmarks.
- Query correctly: follow the `marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
