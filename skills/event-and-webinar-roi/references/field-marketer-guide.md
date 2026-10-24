# Supermetrics workflows for the field marketing manager

This skill helps field marketing managers running localized events — webinars, in-person events, regional roadshows, conference activations — make data-driven decisions on tight timelines. The data needs to inform action this week, often today.

## Core principles

Three principles apply throughout every workflow:

1. **Two-step pattern: pull, then analyze.** Even on tight timelines, don't combine retrieval and analysis. Event campaign data is often messy (inconsistent UTMs, registration tracking split between ad platforms and event platforms). Catching issues early matters more on a short timeline, not less.

2. **Communicate uncertainty.** Field marketing analysis is shaped by small sample sizes — a regional event might have 80 total registrations. The difference between "$45 per registration on LinkedIn" and "$52 per registration on Google" might be statistical noise. When analysis drives budget shifts on a short timeline, name when conclusions are tentative.

3. **Show, don't tell.** Even with small samples, visualizations make trade-offs and trends immediately legible. See "Choosing the right visualization" below.

## Choosing the right visualization

This skill doesn't prescribe specific charts. Decide based on what the data and the question actually are.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| Cost-per-registration comparison across channels | Sorted horizontal bar chart |
| Registration pacing vs target as the event approaches | Line chart with cumulative registrations and dashed target line |
| Regional performance vs national baseline | Paired bars (target region vs rest-of-country) per metric |
| Trend in registrations over time, broken out by channel | Line chart, one series per channel |
| Day-by-day spend vs registrations during event run-up | Dual-axis bar/line chart |
| Geo-targeted ad performance across multiple regions | Sorted bar chart by region, or a small map if locations matter visually |

### Step 2 — What's the headline?

Before building, name the single thing the chart should make obvious. "Google Ads is generating registrations 18% cheaper than LinkedIn, but with smaller sample size" is a headline. "Channel comparison" is not.

Make the headline land within two seconds:
- **Sort by the metric of interest**
- **Highlight the winning channel** in a contrasting color
- **Add a reference line** for the registration target or the national baseline
- **Annotate sample-size caveats** directly on the chart (e.g. "Google: only 18 registrations — directional")

### Step 3 — Render it in the conversation

For data visualizations in chat, render the chart inline with the charting or code-execution capability available in the session.

### Standard color palette

- **Good / improving / positive direction:** `#10b981` (green)
- **Bad / declining / negative direction:** `#ef4444` (red)
- **Neutral / baseline / no-direction comparison:** `#6366f1` (indigo)
- **Flagged / warning / attention:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6`
- **Text:** `#374151`

When sample sizes are small, low-confidence comparisons should be visually distinguished (lower opacity, hatched bars, or a "low confidence" badge) so they don't look like high-conviction findings.

### Skip the chart when

- The reconciliation is just a number ("Google CPL was $45, LinkedIn was $52")
- The deliverable is a quick Slack message to the regional team
- The sample size is so small the chart would imply confidence the data doesn't support

### Ad hoc analysis

For one-off questions outside the bundled workflows ("did email-driven signups outperform ad-driven signups for this event?", "which job titles in the registration list came from which channel?", "what's the no-show rate by channel?"), write fresh analysis code.

## Other capabilities to leverage

**Built-in document skills.** When the deliverable is a file:
- Word documents (post-event summaries, ROI memos) → the `docx` skill
- Spreadsheets (registration tracking, channel breakdowns) → the `xlsx` skill
- Slide decks (post-event leadership readouts) → the `pptx` skill

If one of these skills is available, read its SKILL.md before building.

**Other connectors when they're available.** If the user has Slack connected, regional team updates can be posted to the right channel — this is often the natural delivery for field marketing communication. If Gmail is connected, sales-team updates can become drafts. If Google Calendar is connected, you can look up the event date directly rather than asking.

**Web search.** Useful for industry event-marketing benchmarks (CPR norms by event type), competitive intelligence on what other companies are doing in the same region, or recent platform changes affecting geo-targeting accuracy.

**Clarifying questions.** Use a multiple-choice question tool if one is available — which event platform's registration count is the source of truth, which channels to compare, what counts as the "region" for geo analysis (city, metro, state).

## Things to watch for

**Registration tracking is often split.** Ad platforms report "form submissions," event platforms report "registrations." These rarely match. Ask the user which is the source of truth.

**UTM tagging on event campaigns is unusually error-prone** — event campaigns get spun up quickly with copy-pasted tagging. Before treating data as gospel, check that ad-platform registration counts are within ~15% of the event platform's. If they're further apart, surface the tagging issue first.

**Geo data has lag.** Some platforms report regional breakdowns with 24–48 hour additional lag beyond standard reporting. Surface the latency if the user is asking about yesterday's regional numbers.

**National averages aren't really national — they include the target region.** When measuring regional lift, exclude the target region from the baseline calculation. The geo_lift_analyzer script handles this.

**Sample sizes are small.** A regional event with 80 registrations has limited statistical power. A 20% gap between channels at this scale could easily be noise. Respect the sample-size flag in the recommendation.

**Post-event lift is hard to attribute.** Did the event drive the lift? Or the targeted ads that supported it? Or general regional growth? When reporting lift, note what's confounded and resist the urge to claim the event caused all of it.
