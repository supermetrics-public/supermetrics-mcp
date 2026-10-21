# Supermetrics workflows for the content marketing manager

This skill helps content marketing managers prove the ROI of expensive content (writer time, designer time, sometimes agency spend) and document the specific fixes their web/UX team needs to make. The two recurring problems: pages with high traffic but no conversion, and the question of which content assets deserve paid promotion.

## Core principles

Three principles apply throughout every workflow:

1. **Two-step pattern: pull, then diagnose.** Pull the asset-level data first. Confirm what you got. Then run diagnosis. The diagnosis depends on knowing what's actually in the data.

2. **Be explicit about what counts as a conversion.** "Conversion rate" on a content landing page can mean form fill, content download, demo request, newsletter signup, or page-to-page click-through. Ask the user which one matters for the page being analyzed.

3. **Show, don't tell.** Content audits are dense with page-level data; visualizations make underperformers immediately legible. See "Choosing the right visualization" below.

## Choosing the right visualization

This skill doesn't prescribe specific charts. Decide based on what the data and the question actually are.

### Step 1 — What is the user trying to see?

| Question type | Visualization family |
|---|---|
| Ranking pages by lost-opportunity (traffic × conversion gap) | Sorted horizontal bar chart |
| Pages plotted by two metrics (e.g. traffic vs conversion rate) | Scatter / quadrant chart |
| Trend in page performance over time | Line chart, optionally multi-series for the top pages |
| Content asset comparison (engagement + conversion across assets) | Grouped bar chart or small multiples |
| Distribution of bounce rate or time-on-page across pages | Histogram or box plot |
| Funnel from impression to conversion for a single asset | Funnel chart or descending stacked bars |

The landing-page diagnosis workflow is the prime case for a **quadrant chart**: sessions on the x-axis, conversion rate on the y-axis. Pages in the bottom-right (high traffic, low conversion) are the priority fixes. Pages in the top-right are the winners worth studying. Pages in the bottom-left aren't worth the team's time.

### Step 2 — What's the headline?

Before building, name the single thing the chart should make obvious. "Three top-10 traffic pages convert below 0.5% — fixing them is the highest-leverage move" is a headline. "Page performance data" is not.

Make the headline land within two seconds:
- **Sort by lost-opportunity** (traffic × conversion gap), not by traffic alone or conversion rate alone
- **Highlight the priority pages** in a contrasting color
- **Add reference lines** for the conversion-rate target and the minimum-traffic threshold
- **Annotate the worst offender** with its URL directly on the chart

### Step 3 — Build as inline artifact

For data visualizations in chat, build a React component using Recharts.

### Standard color palette

- **Good / improving / positive direction:** `#10b981` (green)
- **Bad / declining / negative direction:** `#ef4444` (red)
- **Neutral / baseline / no-direction comparison:** `#6366f1` (indigo)
- **Flagged / warning / attention:** `#f59e0b` (amber)
- **Gridlines:** `#f3f4f6`
- **Text:** `#374151`

Color by *business meaning*. Bounce rate at 80% is red (bad), conversion rate at 4% is green (good).

### Skip the chart when

- The output is the web team fix doc (chart goes inside the doc, not in chat — though a thumbnail summary chart is fine)
- The user just wants a sorted list of pages to fix (markdown table is fine)
- The data has fewer than 4 items worth comparing

### Ad hoc analysis

For one-off questions ("which referral sources have the lowest bounce rate on our blog?", "what's the correlation between time-on-page and conversion rate?", "which days of the week drive the highest-engagement traffic to our top pages?"), write fresh analysis code.

## Other Claude capabilities to leverage

**Built-in document skills.** When the deliverable is a file:
- Word documents (the web team fix doc is the prime case) → the `docx` skill
- Spreadsheets (page-by-page audit lists the web team can check off) → the `xlsx` skill
- Slide decks (content marketing reviews) → the `pptx` skill

If one of these skills is available, read its SKILL.md before building.

**Other connectors when they're available.** If the user has Google Drive connected, the fix doc can land directly in Drive. If Slack is connected, a short version of the priority list can be posted to the relevant channel. If a CRM such as HubSpot is connected in Supermetrics, pages can be cross-referenced with downstream MQL data to identify which low-CVR pages are actually low-quality-lead generators.

**Web search.** Useful for current bounce-rate and conversion-rate benchmarks by content type, recent SEO/UX research, or industry-specific norms.

**Clarifying questions.** Use a multiple-choice question tool if one is available (`ask_user_input_v0` on claude.ai, `AskUserQuestion` in Claude Code) for clarifications — which conversion event matters, which page types to include or exclude, what bounce-rate threshold to flag.

## Things to watch for

**GA4 bounce rate is not Universal Analytics bounce rate.** In GA4, bounce rate is the share of sessions that weren't engaged (shorter than 10 seconds by default, no key event, one page view), the inverse of engagement rate. A reader who spends 4 minutes on one blog post is an engaged session, not a bounce. So a high GA4 bounce rate means visitors left almost immediately, a real signal, but judge it by page type and traffic source: paid social traffic bounces more than search. Surface bounce rate together with average engagement time.

**Sample size matters for content.** A page with 50 visits in 30 days and a 0% conversion rate doesn't tell you much. The landing page diagnoser has a minimum-traffic filter (default 500 sessions); don't lower it without reason.

**Content has long tails.** A blog post converting at 0.3% with 100k monthly visitors generates more leads than a page converting at 5% with 800 visitors. Rank by absolute lost-opportunity (sessions × conversion gap), not by conversion rate alone.

**Paid promotion of content has different success criteria than direct response.** A whitepaper ad with 12% CTR but 0.5% lead form completion is *probably* engaging the right audience — the lead form is where the friction lives, not the ad. Don't dismiss high-engagement / low-conversion content ads without checking the post-click experience.

**Attribution for gated content is messy.** A user might download a whitepaper today and book a demo six weeks later via a different channel. Most attribution models won't credit the content. Ask the user what conversion event they're measuring against — content's value is often in leading indicators (downloads, time-on-page, subscriber growth) rather than direct attribution.
