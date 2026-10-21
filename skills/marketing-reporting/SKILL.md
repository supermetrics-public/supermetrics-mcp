---
name: marketing-reporting
description: Leadership-level marketing reporting from live data: 90-day executive overviews, budget pacing and month-end forecasts, cross-channel ROAS benchmarking, efficiency vs goals, CAC and LTV unit economics, annual planning and new-channel business cases, delivered as decks, memos or live Supermetrics Studio dashboards. Use for CMO and board updates, pacing checks and planning.
---

# Marketing reporting and planning

Workflows for marketing leaders and analysts who need a cross-channel picture, a forecast or a plan, and a shareable deliverable.

## Pick the workflow

| Question | Focus | Steps |
|---|---|---|
| The 90-day executive overview | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 1 |
| Budget pacing and financial forecasting | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 2 |
| Cross-channel ROAS benchmarking | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 3 |
| Annual planning support | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 4 |
| New channel investment case | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 5 |
| Marketing efficiency vs goals tracking | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 6 |
| CAC and LTV unit economics | Leadership | [references/marketing-leader-workflows.md](references/marketing-leader-workflows.md) — Workflow 7 |

Read the linked workflow before pulling data. Background for each focus area:

- Live dashboards: when and how to publish to Studio in [references/studio-dashboards.md](references/studio-dashboards.md)
- Leadership: principles, visualization and gotchas in [references/marketing-leader.md](references/marketing-leader.md)

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
