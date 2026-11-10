# Executive marketing report — workflow details

Chart choices and common variations for each workflow in SKILL.md. Read the section for the workflow you are running when you reach its visualization step, or when the user asks for a variant of the workflow.

## The 90-day executive overview

### Chart choices

Options that work well at this layer:
- A small dashboard-style group of headline metric cards (total spend, total revenue, blended ROAS, total conversions) with the period-over-period change next to each
- A simple weekly line chart showing blended ROAS or total spend trend over the 90 days
- A stacked bar showing channel-mix of spend and a paired stacked bar showing channel-mix of revenue (mismatches between the two are the most useful finding)

Pick one — don't build all three. The headline is one thing, not three.

### Common variations

- **Just the executive summary, skip channel deep-dives** — collapse to one summary visualization + one cross-channel comparison + recommendations.
- **Quarter-over-quarter framing** — pull a second 90-day window from the prior quarter and add a QoQ comparison visualization (slopegraph works well for this).
- **Add a specific campaign deep-dive** — drop in one focused slide between the channel-level section and the cross-channel comparison.

## Annual planning support

### Chart choices

The most useful visualization here is a quadrant chart with axes: average monthly ROAS (or another efficiency metric) on the y-axis, ROAS volatility (standard deviation across months) on the x-axis. Dots sized by total annual spend. Quadrants tell the story:
- High ROAS, low volatility (top-left) — the keepers
- High ROAS, high volatility (top-right) — promising but risky
- Low ROAS, low volatility (bottom-left) — predictable but draining
- Low ROAS, high volatility (bottom-right) — sunset candidates

A secondary visualization: stacked bar by month showing channel-mix evolution over the year. Where has the user's channel mix actually drifted?

### Common variations

- **Add a "new channels" scenario** — combine with Workflow 5's analytical output for proposing new platforms.
- **Pipeline contribution overlay** — if HubSpot or Salesforce is connected, overlay pipeline-influenced revenue on top of direct-attribution revenue. Often changes the rank order.
- **Multi-year comparison** — if 24+ months of data is available, show 2-year-over-2-year trends rather than month-by-month.

## New channel investment case

### Chart choices

A useful primary visualization is a stacked or grouped bar chart showing per-channel saturation signals (CPM trend, frequency, CTR trend) over the past 6 months. Channels showing red across multiple signals are saturation cases.

A secondary visualization: a comparison table or bar chart showing the proposed new channel's expected CPM/CPC/conversion rate (from research) against the user's blended current numbers, with the assumption explicitly labeled.

### Common variations

- **For retail media specifically** — Walmart Connect, Amazon Ads, Criteo Retail Media. These have different economics (much higher CPC but lower-funnel intent). Frame as "incremental ROAS during high-purchase-intent moments" rather than head-to-head with social.
- **For audio (Spotify Ads)** — different audience and creative requirements. Surface the production cost as well as the media cost.
- **For B2B with LinkedIn Lead Gen Forms** — if the user is already on LinkedIn Ads, this is about expanding format usage not adding a channel.

## Marketing efficiency vs goals tracking

### Chart choices

A horizontal bar chart with one bar per metric, where each bar shows actual value with the target as a reference line, works well. Color the gap red when over target on a "lower is better" metric (CAC), green when under it. The user sees at a glance which metrics are meeting targets.

A secondary visualization: a small line chart per metric showing the trailing 90-day trend, with the target as a horizontal reference line. Surfaces whether the gap is widening, holding, or closing.

### Common variations

- **Cohort-level CAC** — if the user has month-of-acquisition cohorts, compute CAC per cohort rather than blended. Surfaces whether recent cohorts are getting more expensive to acquire.
- **Channel-attributed CAC** — split blended CAC into per-channel CAC using a defined attribution model. State which model.
- **CAC payback by cohort** — for SaaS or subscription businesses, this is the real question. Cohort revenue over time vs. cohort acquisition cost.

## CAC and LTV unit economics

### Chart choices

Two charts that work together:
- **Cohort LTV curve**: x-axis = months since acquisition, y-axis = cumulative revenue per customer. One line per acquisition cohort. The shape of these curves is the most important visualization in unit economics — flat curves mean low retention, steep curves mean strong retention.
- **CAC vs LTV bars by channel**: paired bars per channel showing CAC and LTV. The gap between the pairs is the channel-level contribution margin.

### Common variations

- **Subscription business** — pull monthly recurring revenue (MRR) cohorts rather than total revenue. The chart becomes MRR retention curves, which is the standard SaaS view.
- **Heavy seasonality** — overlay multiple acquisition-quarter cohorts to see whether different acquisition periods have systematically different LTV.
- **By acquisition channel** — when ad-platform → CRM customer ID linkage is available, compute per-channel LTV. Often shows that the cheapest CAC channels have the worst retention.
