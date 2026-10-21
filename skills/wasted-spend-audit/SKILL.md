---
name: wasted-spend-audit
description: Find wasted ad spend from live data: zero-conversion campaigns and ads, expensive CPAs, budget traps, Google and Microsoft search terms to negate, and the monthly saving. Use for "where am I wasting money", "which search terms should I exclude", "what should I pause" on Google Ads, Microsoft Advertising, Meta and other ad platforms.
---

# Wasted spend audit

## Workflows

### Search term audit and negative keywords
**When to use:** the user runs Google Ads or Microsoft Ads search campaigns and wants to find wasted spend on irrelevant queries, building a negative keyword list to upload to the platform.

**Platforms typically involved:** Google Ads (search campaigns), Microsoft Ads. This workflow doesn't apply to social platforms.

### Step 1 — Pull the search term report
> Pull the search term report from [Google Ads / Microsoft Ads] for the last 30 days, including search term, matched keyword, campaign, ad group, clicks, cost, and conversions.

Fields: Google Ads `Searchterm` (with `Keyword`, `Matchtype`), Microsoft Ads `SearchQuery`. The search term report is the granular log of actual user queries that triggered the ads — distinct from the keyword list, which is what the user bid on. Mismatch between intent and actual query is where waste lives.

### Step 2 — Audit the terms
Use `scripts/search_term_auditor.py`. The script flags queries that:
- Cost more than $X (configurable) with zero conversions
- Have CPC more than 2x the campaign average
- Match obvious irrelevance patterns (competitor brand names, "free", "jobs", "career", "wikipedia" depending on the user's industry context)

The user provides the irrelevance patterns; the script doesn't guess them. If the user doesn't have a list, suggest a starter set based on their industry and confirm.

### Step 3 — Visualize the waste
The question the chart needs to answer: **how much money are these terms costing, and which patterns are the worst?**

A horizontal bar chart of cost-per-term (top 20 worst offenders) is the most useful primary visualization. If the user has clearly grouped patterns (competitor names vs informational queries vs unrelated terms), a stacked bar chart by pattern type surfaces where the largest categories of waste live.

A pie chart or treemap of cost by pattern type works only if there are ≤5 clear categories.

### Step 4 — Build the negative keyword list
Use `assets/negative_keyword_recommendations.md`. For each proposed negative keyword, the doc includes the match type recommendation (exact / phrase / broad), the rationale (cost incurred, related queries impacted), and the campaigns where the negative should be applied.

### Step 5 — Output
Default delivery: the table as a spreadsheet ready for ad-platform upload, using the `xlsx` skill. Most ad platforms accept negative keyword bulk uploads in CSV/XLSX format. If the user prefers, deliver markdown inline.

### Common variations
- **Just one campaign** — filter to a single campaign in step 1.
- **Smaller wins** — lower `--min-cost` below its $50 default (e.g. $20).
- **Already-running negatives** — if the user provides their current negative keyword list, exclude those terms from the recommendations.

---

## Background

Chart choices, principles and gotchas for this area: [references/performance-marketer-guide.md](references/performance-marketer-guide.md).

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
