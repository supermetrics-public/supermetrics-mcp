# Supermetrics workflows for the performance marketer

This skill helps performance marketers analyze paid campaigns across Google Ads, Facebook Ads, LinkedIn Ads, TikTok Ads, and Microsoft Ads. The workflows are designed to produce decisions a media buyer can act on today — not dashboards that need to be interpreted.

## Core principles

Three principles apply throughout every workflow:

1. **Two-step pattern: pull, then analyze.** Don't combine data retrieval and complex analysis into a single tool call. Pull the raw data first, confirm what was returned, then run analysis as a separate step. This catches data issues early.

2. **Show, don't tell.** When the analysis produces numbers worth comparing — across time periods, channels, ads, or anything else — build a visual. The user's time-to-decision is shorter with a chart than with prose. See "Choosing the right visualization" below.

3. **One serious query at a time.** Don't fire several heavy queries in parallel. Sequence the work.

## Choosing the right visualization

This skill doesn't prescribe specific charts for specific workflows. Instead, decide visualization based on what the data and the question actually are. Use this framework:

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| Comparison across discrete items (campaigns, ads, channels) | Bar chart (horizontal if the labels are long) |
| Change over time | Line chart (or area if showing accumulation) |
| Relationship between two metrics (e.g. frequency vs CTR) | Scatter plot |
| Distribution of a single metric across many items | Histogram or sorted bar chart |
| Two-dimensional categorical breakdown (e.g. day-of-week × hour) | Heatmap |
| Part-to-whole when there are few parts (<6) | Stacked bar — avoid pie charts |
| Ranking with magnitude | Sorted horizontal bars |
| Trend with target/threshold | Line chart with a reference line |

### Step 2 — What's the headline?

Before building, name the single thing the chart should make obvious. "CTR is dropping on every campaign except one" is a headline. "Performance data" is not.

The chart's design should make the headline land within two seconds of looking at it. Techniques that help:
- **Sort by the metric of interest** (don't leave alphabetical order)
- **Highlight one bar or one point** in a contrasting color when there's a clear standout
- **Add a reference line** for the baseline, target, or account average
- **Annotate the worst offender or the winner** directly on the chart

If you can't name the headline, the visualization isn't ready — the analysis probably isn't either.

### Step 3 — Build it as an inline artifact

For data visualizations in chat, build a React component using Recharts (available in the artifact environment). Inline artifacts are the right default — they render immediately and the user doesn't have to leave the conversation.

For one-off conceptual visuals (e.g. funnel diagrams, flowcharts) that aren't strictly chart-able, use a diagram or widget tool if one is available, otherwise a simple inline SVG or Mermaid diagram.

### Standard color palette

For consistency across charts in the same conversation, use this palette:
- **Good / improving / positive direction:** `#10b981` (green)
- **Bad / declining / negative direction:** `#ef4444` (red)
- **Neutral / baseline / no-direction comparison:** `#6366f1` (indigo)
- **Flagged / warning / attention:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6`
- **Text:** `#374151`

Color by *business meaning*, not by sign. If CPC is up 20%, the bar is red because CPC up is bad — even though the number is positive. If CPA is down 15%, the bar is green for the same reason.

### Skip the chart when

- The answer is 1–3 numbers (just state them: "Total spend: $12k, conversions: 240, CPA: $50")
- The data is text-heavy (ad copy, recommendations) — those are prose or markdown
- The output is going into a Word doc or slide deck where the chart will live inside the file, not in chat

### Ad hoc analysis

The bundled scripts handle repeatable patterns. For one-off questions ("does ad copy length correlate with CPA in this dataset?" or "is there a time-of-day effect on weekend traffic?"), write fresh analysis code in the chat — pull the data, compute the answer, build the right visualization for it. Don't force a one-off question through a script that wasn't designed for it.

## Other Claude capabilities to leverage

**Built-in document skills.** When a workflow's final step is a file deliverable:
- Word documents (optimization reports, recommendations) → use the `docx` skill, if available
- Spreadsheets (campaign performance tables for sorting/filtering) → use the `xlsx` skill, if available
- Slide decks (creative review presentations) → use the `pptx` skill, if available

Read the relevant SKILL.md *before* writing any code or building any file.

**Other connectors when they're available.** If the user has CRM connectors active (HubSpot, Salesforce), you can pull lead-quality data and join it with ad-platform CPA — answering "are we buying real leads or just clicks?" If they have Gmail, you can save email drafts directly. If they have Slack, you can post update messages to the right channel. These are opportunistic moves — only use them when they're already available in the session, never assume they exist.

**Web search.** Useful for industry benchmarks, recent platform policy changes (e.g. "new Meta attribution defaults"), or competitive context. Don't over-use it — most performance marketing answers come from the user's own data.

**Clarifying questions.** When a multiple-choice question would resolve ambiguity (attribution window, time period, scope), use a multiple-choice question tool if one is available (`ask_user_input_v0` on claude.ai, `AskUserQuestion` in Claude Code). It renders tappable buttons rather than asking the user to type — much faster on mobile.

## Things to watch for

**Naming conventions vary.** When a user references a campaign by name, search broadly. If multiple campaigns match, list them and let the user pick.

**Time windows matter.** "Last 7 days" can mean rolling 7-day or calendar week. When ambiguous, ask once, then remember the convention.

**Acting on recommendations.** These workflows analyze and recommend. When the user wants to apply a change (pause, budget shift, negatives), hand off to the `campaign-management` skill, with explicit approval for each change.

**Recommendations should be specific.** "Pause underperforming ads" is not a recommendation. "Pause ad ABC123 (CPA $84, 3.2x account average) and shift its $200/day to ad XYZ789 (CPA $19)" is. Always aim for the second version.

**Currency and timezone.** Supermetrics returns data in the account's currency and timezone. For users working across multiple markets, surface this before computing aggregates.

**When data looks suspicious.** A sudden zero-conversion day or 10x CPC spike is more often a tracking break than a real performance issue. Flag anomalies before recommending action — the right next step might be to fix tracking, not to pause spend.
