# Supermetrics workflows for marketing analytics and leadership

This skill helps marketing leaders and analysts produce macro-level cross-channel analysis: the quarterly review, the budget pacing forecast, the cross-platform ROAS comparison. The output of these workflows lands in front of CMOs, finance partners, and boards — so the workflows are tuned for "decision-ready" outputs, not data dumps.

## Core principles

Three principles apply throughout every workflow:

1. **Two-step pattern: pull, then analyze.** Don't combine retrieval and synthesis into a single tool call. Pull the data, confirm what you got with a one-line summary, then run the analysis. Leadership deliverables especially benefit from this — if the data is wrong, the recommendations built on it are wrong, and the leader won't catch it until the meeting.

2. **Show, don't tell.** Leadership audiences scan deliverables; they don't read them line by line. When the analysis produces numbers worth comparing — across channels, across time, across budget scenarios — build a visual. See "Choosing the right visualization" below.

3. **One serious query at a time.** Multi-channel pulls over 60–90 days can be heavy. Sequence the queries.

## Choosing the right visualization

This skill doesn't prescribe specific charts for specific workflows. Instead, decide visualization based on what the data and the question actually are.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| Channel performance side-by-side (spend, ROAS, conversions) | Grouped bars or small multiples — one mini-chart per channel |
| Trend over time (spend, conversions, ROAS week-by-week) | Line chart, optionally with a reference line for target |
| Budget pacing vs target | Line chart with cumulative spend and a dashed target line; shade gap red or green |
| Ranking channels by efficiency | Sorted horizontal bar chart |
| Part-to-whole when there are few channels (<6) | Stacked bar (not pie) — and only when share matters, not just totals |
| Two-dimensional categorical comparison | Heatmap (e.g. ROAS by channel × month) |
| Quarter-over-quarter comparison | Side-by-side bars or slopegraph |
| Forecast vs actual | Line chart with actuals as solid line, forecast as dashed line |

### Step 2 — What's the headline?

Before building, name the single thing the chart should make obvious. "Spend climbed but conversions held flat — efficiency is sliding" is a headline. "Q3 marketing metrics" is not.

Make the headline land within two seconds of looking at the chart:
- **Sort by the metric of interest** (alphabetical order hides the answer)
- **Highlight one bar or one segment** in a contrasting color when there's a clear standout
- **Add a reference line** for target, baseline, or last-period value
- **Annotate the winner or the worst offender** directly on the chart

If you can't name the headline, the chart isn't ready. Often that means the analysis isn't ready either.

### Step 3 — Build it as an inline artifact

For data visualizations in chat, build a React component using Recharts (available in the artifact environment). For visualizations destined for a slide deck or document, embed them inside the file using the relevant built-in skill (see below).

### Standard color palette

For consistency across charts in the same conversation:
- **Good / improving / positive direction:** `#10b981` (green)
- **Bad / declining / negative direction:** `#ef4444` (red)
- **Neutral / baseline / no-direction comparison:** `#6366f1` (indigo)
- **Flagged / warning / attention:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6`
- **Text:** `#374151`

Color by *business meaning*, not by sign. If spend is +30%, the bar is red if that's overspend, green if it's a deliberate scale-up.

### Skip the chart when

- The answer is 1–3 numbers (just state them in headline form)
- The deliverable is a narrative document where prose is more appropriate
- The user explicitly asked for a table or a spreadsheet

### Ad hoc analysis

For one-off questions the bundled scripts weren't designed for ("what's the spend concentration across channels — top 3 vs the rest?", "is there a launch-month effect on new campaigns?"), write fresh analysis code in chat. Pull the data, compute the answer, build the visualization that fits the question.

## Other Claude capabilities to leverage

**Built-in document skills.** When the deliverable is a file:
- Slide decks (the executive overview is the prime case) → use the `pptx` skill, if available
- Word documents (handouts, memos, board reports) → use the `docx` skill, if available
- Spreadsheets (pacing tables, channel comparisons) → use the `xlsx` skill, if available

Read the relevant SKILL.md *before* building any file. The slide-deck workflow especially depends on this — slides have visual rules that don't apply to docs.

**Other connectors when they're available.** If the user has Google Drive connected, the executive deck or memo can land directly in Drive in a chosen folder. If Gmail is connected, the CMO email can become a draft. If Slack is connected, a short version of the headline can be posted to a leadership channel. These are opportunistic — only use them when they're already available.

**Web search.** Useful for industry benchmarks (CPA / ROAS norms for the user's category), recent platform changes that might explain anomalies, or context for board-level narratives.

**Clarifying questions.** When you need a multiple-choice answer to proceed (which attribution model, which time window, which currency conversion approach), use a multiple-choice question tool if one is available (`ask_user_input_v0` on claude.ai, `AskUserQuestion` in Claude Code). Cleaner than asking in prose on mobile.

## Things to watch for

**Attribution is political.** Facebook's 7-day-click / 1-day-view ROAS, data-driven ROAS in Google Ads, and a multi-touch model the company runs separately will all show different numbers. When the user asks "which channel has the best ROAS," surface the attribution model behind the comparison before recommending shifts.

**Run rates lie when spend is lumpy.** A campaign launched mid-month and just hitting full pace will under-forecast with a naive daily-average. The pacing script uses the month-to-date daily average, so flag the assumption explicitly; if spend ramped mid-month, project from the recent daily rate yourself.

**Quarterly reviews need a "what we tried" section.** The temptation is to report only what worked. Leaders trust analysts more when the review names what was tested that didn't work and what was learned. Include this when the data supports it.

**Currency normalization.** When aggregating across markets, convert to a single currency before computing ROAS or ranking (some sources offer converted cost fields, e.g. Google Ads `Cost_usd`). Surface the conversion approach (period-average, end-of-period, FX-fixed for planning) — don't assume.

**Acting on recommendations.** These workflows analyze and recommend. When the user wants to apply a budget change, hand off to the `campaign-management` skill, with explicit approval for each change.

**Budget-shift recommendations need guardrails.** A naive "shift 30% from the worst channel to the best" can backfire because channels often have diminishing returns above their current spend ceiling. Default to 15–20% and explicitly call out that the recommendation assumes the destination has remaining capacity.
