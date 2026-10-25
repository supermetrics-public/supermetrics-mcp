---
name: budget-pacing-allocation
description: Check ad budget pacing and plan reallocation from live data: month-to-date spend vs budget with a month-end forecast per channel, cross-channel ROAS benchmarking, and modelled budget shifts with diminishing returns. Use for "are we on track", "where should the next euro go" and reallocation proposals.
---

# Budget pacing and allocation

## Workflows

### Budget pacing and financial forecasting
**When to use:** the user wants to know if monthly (or quarterly) ad spend is on track and what month-end totals will be. Often mid-month when there's still time to course-correct.

**Platforms typically involved:** whichever platforms have meaningful spend. For B2B usually Google Ads, LinkedIn Ads, Facebook Ads. For ecommerce, add Pinterest and TikTok.

### Step 1 — Pull month-to-date data
> Pull the daily ad spend and total conversions for the current period across [the connected ad platforms].

Daily granularity is required — the pacing script computes the run rate as the month-to-date daily average. If spend ramped mid-month, say so and project from the recent daily rate instead.

### Step 2 — Forecast period-end
Use `scripts/budget_pacing.py`. Pass in the period target budget per channel; ask the user if they haven't shared it. The script outputs period-end projections and the variance vs. target.

### Step 3 — Visualize pacing
The question the chart needs to answer: **are we on track, and where's the risk?**

The most useful visualization for this is usually a line chart per channel: cumulative spend (solid line) vs. target trajectory (dashed line, a straight line from $0 at period start to target at period end). Channels below the dashed line are underspending; above it are overspending. Shade the gap red or green based on which side of the target the channel is on.

Alternative when the user has many channels (8+): a horizontal bar chart of variance percent, sorted by absolute variance, color-coded by direction.

### Step 4 — Flag pacing issues
The script flags channels off-pace by more than ±15% (configurable). Surface the flagged channels with their projected period-end and a one-line recommendation per channel — slow down bidding, increase daily caps, redistribute budget.

### Step 5 — Output
Default delivery is the chart in chat plus a markdown summary. Pacing is checked repeatedly, so offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants a spreadsheet for the planning team, use the `xlsx` skill.

### Common variations
- **"What if we hold spend flat for the rest of the period?"** — re-run the projection with the daily run rate frozen. The script supports a `--freeze-from` date.
- **Compare to last period's pacing curve, not just the target** — pull prior-period daily data and overlay both lines on the chart. Often more useful than the abstract target line.
- **Project conversions, not just spend** — the script does both. If conversion volume is the actual concern, lead with that visualization.

---

### Cross-channel ROAS benchmarking
**When to use:** the user is deciding how to allocate next period's budget across platforms and needs a defensible cross-channel comparison.

**Platforms typically involved:** Google Ads, Facebook Ads, LinkedIn Ads at minimum. For ecommerce: add TikTok, Pinterest, sometimes Amazon Ads.

### Step 1 — Pull the data
> Pull the total spend, revenue, and ROAS for the last 60 days across [the connected ad platforms].

Surface the attribution model for each platform. If the user doesn't know, name the issue explicitly in the deliverable — comparing Facebook's 7-day-click / 1-day-view ROAS to data-driven ROAS in Google is apples to oranges and a finance partner will spot it.

### Step 2 — Rank and compare
Use `scripts/roas_ranker.py`. The script ranks channels, computes the best-to-worst spread, and outputs a budget-reallocation recommendation with the diminishing-returns caveat baked in.

### Step 3 — Visualize the ranking
The question the chart needs to answer: **how different are the channels in efficiency, and what's the recommendation?**

A sorted horizontal bar chart of ROAS by channel works well as the primary visualization — bars sorted descending, the top performer highlighted, a reference line at blended ROAS so the user can see which channels are pulling weight vs. dragging.

If the user wants more dimension, a scatter of spend (x-axis) vs ROAS (y-axis), dots labeled by channel, makes the "efficient at low scale vs. efficient at scale" distinction immediately visible. Channels in the top-right (high spend, high ROAS) are the keepers; bottom-right (high spend, low ROAS) need attention.

### Step 4 — Justify the recommendation
Build from `assets/budget_reallocation_memo.md`. The memo must contain the size of the proposed shift in dollars (not just percent), and the caveat about diminishing returns on the receiving channel. Finance teams flag this almost reflexively; include it preemptively.

### Step 5 — Output
Default is the chart plus the memo inline as markdown. If the ranking will be revisited, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants a Word doc to send to finance, use the `docx` skill.

### Common variations
- **CPA instead of ROAS** — pass `--metric cpa` to the ranker. The chart logic flips (lower bars are better).
- **Compare to industry benchmarks** — Supermetrics doesn't ship benchmarks, but web search can pull recent industry data for the user's category. Overlay as a reference line on the chart.
- **Different shift percentage** — pass `--shift-pct` to the ranker (default 20%). The memo template renders whatever percentage the script was run with.

---

### Budget reallocation modeler
**When to use:** the user has current ROAS by channel and wants to model the optimal budget shift for next period, accounting for the reality that more spend on a winner doesn't produce the same ROAS at higher volume.

**Platforms typically involved:** all connected ad platforms with meaningful spend.

### Step 1 — Pull current performance
> Pull the last 30-60 days of spend and revenue (or conversions × value) by channel from [the connected ad platforms]. Include daily breakdown to surface diminishing-returns signals.

Daily granularity matters — the script needs it to detect whether ROAS holds at higher spend or compresses.

### Step 2 — Get the target spend
Ask the user (with a multiple-choice question tool if available): what's the total budget for next period? Same as current, ±10%, or a specific number?

### Step 3 — Run the optimizer
Use `scripts/budget_optimizer.py`. The script:
1. Computes current channel ROAS and spend
2. Estimates a diminishing-returns dampening factor per channel from the daily data (channels with higher spend variance and stable ROAS get dampened less; channels with ROAS that already shows compression get dampened more)
3. Models reallocation toward higher-ROAS channels, but applies the dampening so the model doesn't naively dump all budget into the single winner
4. Outputs proposed spend per channel and projected total revenue

This is a model, not a prediction. The script's output is "given these assumptions, here's the math" — not "this will happen."

### Step 4 — Visualize the recommendation
The question the chart needs to answer: **what's the proposed shift, and what's the projected impact?**

Two visualizations work well together:
- A paired bar chart per channel: current spend vs proposed spend. Sorted by absolute shift magnitude.
- A waterfall chart showing the projected revenue impact: starting from current total revenue, adding contributions from spend increases and subtractions from spend decreases, ending at projected new total.

### Step 5 — Frame the assumptions
Build from `assets/budget_reallocation_plan.md`. The plan must include:
- Specific dollar amounts per channel (current vs proposed)
- Projected revenue impact with the assumption disclosure
- The dampening logic (so finance can challenge the model intelligently)
- A 2-week checkpoint date — when to verify whether the projection is tracking

### Step 6 — Output
Default delivery: chart + plan inline as markdown. If the deliverable is going to finance for approval, use the `docx` skill.

### Common variations
- **Constrained channels** — if some channels have fixed budgets (e.g. a contracted minimum spend on a platform), pass them as `--locked` to the script. The optimizer will only reallocate the unconstrained portion.
- **Aggressive vs conservative shifts** — pass `--max-shift-pct` (default 25%) to cap how much any single channel can move. Conservative finance teams want smaller shifts.
- **Goal: total conversions, not revenue** — pass `--metric cpa`. The optimizer flips to minimizing CPA-weighted spend rather than maximizing revenue.

---

## Ad hoc analysis

The bundled scripts handle the repeatable patterns above. When the user asks something the scripts weren't designed for — "is there a time-of-day effect on this campaign?", "does ad copy length correlate with CPA?", "what's the spend distribution across ad sets — concentrated or spread?" — write fresh analysis code, run it, and build whatever visualization fits the question.

The "Choosing the right visualization" framework in the SKILL.md gives you the decision tree for which chart family fits which type of question. Apply it case by case.

---

## A note on data freshness

Platforms report with different delays. Facebook is close to real time; LinkedIn can lag up to 24 hours. Google Ads and Meta credit conversions back to the click or impression date, so the last few days keep filling in until the attribution window closes. When the user is making same-day decisions, surface this so they know what "yesterday's data" actually means.

## Background

Chart choices, principles and gotchas for this area: [references/marketing-leader-guide.md](references/marketing-leader-guide.md), [references/performance-marketer-guide.md](references/performance-marketer-guide.md).

## Before you analyze

- Load saved targets, budgets and known context with `get_business_context` and judge results against them, not generic benchmarks.
- Query correctly: follow the `marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
