---
name: b2b-funnel-and-content
description: B2B funnel, content and event analysis from live data: reconcile ad platform conversions with GA4 and CRM, lead-gen efficiency, pipeline by channel, MQL-to-SQL by source, ABM, email and webinar funnels, landing pages, SEO and Search Console, content attribution, newsletters, video, and event ROI and geo-lift. Use for demand gen, content marketing and field or event marketing questions.
---

# B2B funnel, content and events

Workflows for demand gen, content and field marketers that connect ad spend to traffic, leads, pipeline and revenue.

## Pick the workflow

| Question | Focus | Steps |
|---|---|---|
| The traffic-to-conversion audit | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 1 |
| Lead generation efficiency comparison | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 2 |
| Pipeline contribution by channel | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 3 |
| MQL-to-SQL conversion rate by source | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 4 |
| ABM campaign performance | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 5 |
| Email + ads integrated funnel | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 6 |
| Webinar / gated content funnel analysis | Demand gen | [references/demand-gen-workflows.md](references/demand-gen-workflows.md) — Workflow 7 |
| Landing page performance diagnosis | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 1 |
| Paid promotion content analysis | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 2 |
| Organic content and SEO performance audit | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 3 |
| Content-to-conversion attribution | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 4 |
| Newsletter and email content performance | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 5 |
| Video content performance | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 6 |
| Content gap analysis | Content | [references/content-marketer-workflows.md](references/content-marketer-workflows.md) — Workflow 7 |
| Pre-event channel ROI | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 1 |
| Post-event geo-targeted follow-up | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 2 |
| Multi-event portfolio analysis | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 3 |
| Event-driven pipeline tracking | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 4 |
| Account engagement scoring | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 5 |
| Webinar funnel deep dive | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 6 |
| Partner / co-marketing event ROI | Field & events | [references/field-marketer-workflows.md](references/field-marketer-workflows.md) — Workflow 7 |

Read the linked workflow before pulling data. Background for each focus area:

- Demand gen: principles, visualization and gotchas in [references/demand-gen.md](references/demand-gen.md)
- Content: principles, visualization and gotchas in [references/content-marketer.md](references/content-marketer.md)
- Field & events: principles, visualization and gotchas in [references/field-marketer.md](references/field-marketer.md)

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
