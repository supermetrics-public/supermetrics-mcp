---
name: campaign-performance-review
description: Review paid campaign performance from live data: week-over-week changes with causes and actions, a daily morning check (yesterday vs the 7-day average), and attribution differences between platforms. Use for weekly reviews, daily standups and "what changed" questions across Google Ads, Meta, LinkedIn, TikTok and Microsoft Ads.
---

# Campaign performance review

## Workflows

### Week-over-week campaign optimization
**When to use:** the user wants to spot efficiency decline (rising CPC, dropping conversion rate, climbing CPA) before it eats the monthly budget. Common phrasings: "compare last week to the week before," "is performance dropping," "what changed in the campaign."

**Platforms typically involved:** LinkedIn Ads, Facebook Ads, Google Ads. Confirm if the user has specific platforms in mind.

### Step 1 — Clarify the comparison (only if ambiguous)
If the user hasn't specified the time window or scope, use a multiple-choice question tool if available to clarify:
- Time window: rolling 7-day, calendar week, last 30 days, etc.
- Scope: campaign level, ad set level, or ad level

Skip if the user's phrasing already makes it obvious.

### Step 2 — Pull the data
> Pull performance data for the [campaign name] running on [platforms] for [current period] and [prior period]. Include spend, impressions, clicks, CTR, CPC, conversions, and CPA.

If the user mentioned specific KPIs (ROAS, leads, video views), include those too.

### Step 3 — Run the comparison
Use `scripts/wow_comparison.py` to compute the deltas. The script flags metrics that moved more than ±10% by default.

### Step 4 — Visualize the changes
Build an inline chart. The question the chart needs to answer: **what changed, and by how much?**

Decide the chart based on what's being compared:
- Comparing the same metrics across two time periods, one campaign at a time → horizontal bar chart of percentage changes, one bar per metric, color by business meaning (improvement vs decline)
- Comparing multiple campaigns side-by-side → grouped bars or small multiples (one mini-chart per campaign)
- Trend over many weeks rather than two snapshots → line chart with a reference line for the baseline period

Whatever you build, sort by the metric of interest (don't leave alphabetical), highlight flagged metrics with an outline or marker, and name the headline above the chart ("CTR dropped on 4 of 5 campaigns this week" — not "Performance data").

### Step 5 — Recommendations
> Generate recommendations: which specific ads to pause, where to shift the remaining budget, and what demographic segments are overperforming.

Build from `assets/optimization_doc.md`. Be specific: name ads, give dollar amounts, identify segments by their actual labels (age 25–34, mobile, iOS).

### Step 6 — Package the deliverable
Default delivery is inline markdown — fast, no friction. If the user reviews this weekly, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user asks for something they can share or save, the right tool depends on what they want:
- A Word doc to send around → use the `docx` skill
- A spreadsheet to sort and filter → use the `xlsx` skill
- A message to post in Slack (if a Slack connector is available) → adapt the recommendation to Slack tone (shorter, no formal greeting)

Don't overwhelm the user with options up front. Pick the sensible default and let them redirect.

### Common variations
- **By ad set instead of campaign** — re-run step 2 grouped by ad set (Facebook Ads `adset_name`, Google Ads `Adgroupname`).
- **Just one platform** — drop the cross-channel framing in step 4.
- **Compare to last month instead** — swap the time windows in step 2; the rest is identical.
- **Add lead quality from a CRM** — if a HubSpot or Salesforce connector is active, pull MQL conversion rate by source and join with the ad-platform CPA. The combined view ("$50 ad CPA but only 8% become MQLs vs another channel's $80 ad CPA with 35% MQLs") is more useful than ad-platform CPA alone. Skip if the CRM connector isn't available.

---

### Daily morning standup check
**When to use:** the user wants a quick scan of what happened yesterday across all paid channels — anomalies, wins, things to investigate. The most common "I just got to my desk, give me the headlines" workflow.

**Platforms typically involved:** all connected ad platforms (Google Ads, Facebook Ads, LinkedIn Ads, TikTok Ads, Microsoft Ads, etc.).

### Step 1 — Pull yesterday and the trailing baseline
> Pull daily spend, impressions, clicks, conversions, and conversion value for the last 8 days (yesterday plus the 7 days before) across all connected ad platforms.

The baseline matters — yesterday in isolation is meaningless. The script computes the trailing average from the daily rows; the comparison is what surfaces anomalies.

### Step 2 — Run the daily check
Use `scripts/daily_check.py`. The script computes yesterday-vs-baseline ratios per channel and flags anomalies on:
- Spend ±25% off the daily baseline (over- or under-pacing)
- CPA ±30% off (cost spike)
- Zero conversions on a channel that normally produces conversions (tracking issue suspected)
- ROAS down >20% (efficiency drop)

### Step 3 — Visualize the morning view
The question the chart needs to answer: **what's normal, what's not, and what needs my attention?**

A horizontal bar chart of percent-deviation-from-baseline per channel works as the primary visualization, with three color zones: green within ±10%, amber ±10–25%, red beyond ±25%. The user scans for red bars first.

When the user has many channels (8+), a small-multiples grid — one mini bar chart per metric (spend, CPA, ROAS) with channels on the y-axis — surfaces patterns by metric rather than by channel.

### Step 4 — Action list
Build from `assets/daily_standup_summary.md`. The summary has three sections: **what happened** (the headline), **what to act on today** (specific campaigns or ad sets, with the action), **what to watch** (early signals that aren't yet actionable).

### Step 5 — Output
Default delivery: inline markdown. For a check the user reopens every morning, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants this delivered as a recurring Slack message and a Slack connector is active, format it for Slack tone (shorter, no formal greeting) and offer to post.

### Common variations
- **Just one platform** — filter to a single platform in step 1.
- **Weekly instead of daily** — swap the time windows: pull last 7 days and compare to the prior 7-day average.
- **Custom anomaly thresholds** — pass `--spend-threshold`, `--cpa-threshold`, `--roas-threshold` to the script.

---

### Cross-channel attribution comparison
**When to use:** the user is reconciling different views of conversion data — Facebook's pixel says one thing, GA4 says another, the platform's data-driven attribution says a third. Common before any cross-channel budget decision.

**Platforms typically involved:** Google Ads, Facebook Ads, LinkedIn Ads + GA4 (mandatory for the comparison). Mobile attribution platforms (AppsFlyer, Adjust, Branch) if the user has mobile apps.

### Step 1 — Pull each view
> Pull the last 30 days of conversion counts per channel from [Google Ads, Facebook Ads, LinkedIn Ads] using each platform's default attribution. Then pull the same channels' conversions from Google Analytics 4 using GA4's default attribution.

Check each platform's attribution setting — Facebook Ads `attribution_setting` (default 7-day click / 1-day view), Google Ads `AttributionModel` (ConversionAction report; data-driven by default); GA4 reporting defaults to data-driven. If any are customized, note it explicitly.

### Step 2 — Reconcile
Use `scripts/attribution_comparator.py`. The script joins on channel name and computes the divergence between each platform's self-reported conversions and GA4's view. Flags channels where the gap exceeds ±25%.

### Step 3 — Visualize the disagreement
The question the chart needs to answer: **which channels disagree most between platform-reported and GA4-measured views, and how does this affect ROAS?**

A paired bar chart per channel works well: two bars per channel, one for platform-reported conversions and one for GA4-measured. Big gaps between the pairs are the story. Color the gaps amber when they exceed the 25% threshold.

A secondary visualization useful here: a slopegraph showing how each channel's *rank* changes between the two views. Facebook might be the #1 channel by Facebook's own attribution but the #3 channel by GA4. This rank-change view is more useful for budget decisions than absolute numbers.

### Step 4 — Frame the implication
The point of this analysis isn't to declare one view correct. It's to make the disagreement visible before a budget decision. The deliverable names what the user can and can't conclude:
- Comparing channels using each platform's own attribution → invalid (apples to oranges)
- Comparing channels using a single attribution lens (GA4 or a third-party MMM) → valid
- Estimating "true" conversion contribution → requires more than these two views; suggest incrementality testing or media mix modeling if the stakes warrant

### Step 5 — Output
Default: chart + framing inline as markdown. If the deliverable is going to a finance or leadership audience, use the `docx` skill for a clean memo.

### Common variations
- **Add mobile attribution** — if AppsFlyer, Adjust, or Branch is connected, add their view as a third comparison column.
- **Include a third-party MMM result** — if the user has results from a media mix model, layer those as a fourth view to show the spread.
- **Per-conversion-type breakdown** — if the user tracks multiple conversion events (purchases, leads, signups), run the comparison per type. Sometimes one type tracks cleanly across platforms and another doesn't.

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
