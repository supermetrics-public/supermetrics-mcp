---
name: executive-marketing-report
description: Leadership-level marketing reporting from live data: 90-day executive overviews, efficiency vs goals, CAC and LTV unit economics, annual planning and new-channel business cases, delivered as decks, memos or live Supermetrics Studio dashboards. Use for CMO, board and quarterly updates.
---

# Executive marketing report

## Workflows

### The 90-day executive overview
**When to use:** the user is preparing a quarterly review or cross-channel summary for a CMO, leadership team, or board. The output is usually a slide deck, often accompanied by a printable handout.

**Platforms typically involved:** LinkedIn Ads, Facebook Ads, Google Ads at minimum, often expanded to TikTok Ads, Microsoft Ads, and any display or programmatic platforms the user runs.

### Step 1 — Pull the data
> Pull the last 90 days of performance data from [the connected ad platforms]. Include spend, impressions, clicks, conversions, conversion value or revenue, CPA, and ROAS, broken down by channel and by week.

Weekly granularity matters — trends are visible, not just totals. Leaders often have specific KPIs (MQLs, SQLs, pipeline value) they want surfaced; confirm before going deeper.

### Step 2 — Visualize the headline
The first chart the user sees should answer: **what's the story this quarter?** Build a high-level summary visualization before drilling into channel-level detail.

Options that work well at this layer:
- A small dashboard-style group of headline metric cards (total spend, total revenue, blended ROAS, total conversions) with the period-over-period change next to each
- A simple weekly line chart showing blended ROAS or total spend trend over the 90 days
- A stacked bar showing channel-mix of spend and a paired stacked bar showing channel-mix of revenue (mismatches between the two are the most useful finding)

Pick one — don't build all three. The headline is one thing, not three.

### Step 3 — Channel-level detail
For each channel with meaningful spend, surface the key metrics: spend, revenue, conversions, ROAS, CPA, share of total spend, share of total revenue.

When the comparison is "which channel is pulling its weight?", a side-by-side chart of spend-share vs revenue-share per channel makes mismatches obvious immediately — channels well above the diagonal are over-delivering, channels below it are under-delivering.

### Step 4 — Recommendations
Use `scripts/exec_summary_builder.py` to produce a structured outline of the deck content. The script handles the slide skeleton; the user's analysis fills in the callouts and recommendations.

Build from `assets/exec_deck_outline.md`. Recommendations should be specific: name a channel, name a dollar amount or percent shift, name the expected impact.

### Step 5 — Build the deck
Use the `pptx` skill, if available, to produce the actual `.pptx` file. Read the SKILL.md first — slides have layout rules and visual conventions that don't apply to documents.

Embed the visualizations from steps 2 and 3 inside the deck. Don't put long text on slides — bullets and callouts only. The deck is a talking aid.

### Step 6 — Companion deliverables (only if asked)
For a quarterly review the team will reopen, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants a handout to accompany the deck, use the `docx` skill to produce a Google Doc–style summary. If they want a CMO email, build from `assets/cmo_email.md` and deliver inline as markdown (so they can edit before sending). If Gmail is connected, offer to save as a draft.

### Common variations
- **Just the executive summary, skip channel deep-dives** — collapse to one summary visualization + one cross-channel comparison + recommendations.
- **Quarter-over-quarter framing** — pull a second 90-day window from the prior quarter and add a QoQ comparison visualization (slopegraph works well for this).
- **Add a specific campaign deep-dive** — drop in one focused slide between the channel-level section and the cross-channel comparison.

---

### Annual planning support
**When to use:** the user is building the annual marketing budget proposal for next year. The output goes to the CFO or CEO as a defensible recommendation for how to allocate marketing dollars across channels.

**Platforms typically involved:** all connected ad platforms. Optionally CRM (HubSpot, Salesforce) for pipeline contribution, ecommerce backends (Shopify, etc.) for revenue context.

### Step 1 — Pull the long view
> Pull the last 12-15 months of monthly performance data per channel from [the connected ad platforms]. Include spend, conversions, revenue, ROAS, and CPA.

12+ months captures seasonality. If the user can pull 24 months, even better — but most teams' data hygiene gets messy beyond a year.

### Step 2 — Classify channels by behavior
Use `scripts/annual_planner.py`. The script classifies each channel into one of four buckets based on stability and growth:
- **Stable performer** — flat ROAS, predictable volume. Safe to plan budget on.
- **Growing channel** — improving ROAS or growing volume. Candidate for budget expansion.
- **Declining channel** — deteriorating ROAS or volume. Candidate for budget reduction or sunset.
- **Volatile channel** — high variance, hard to predict. Plan conservatively.

### Step 3 — Visualize the channel portfolio
The question the chart needs to answer: **which channels can we count on next year, and which are at risk?**

The most useful visualization here is a quadrant chart with axes: average monthly ROAS (or another efficiency metric) on the y-axis, ROAS volatility (standard deviation across months) on the x-axis. Dots sized by total annual spend. Quadrants tell the story:
- High ROAS, low volatility (top-left) — the keepers
- High ROAS, high volatility (top-right) — promising but risky
- Low ROAS, low volatility (bottom-left) — predictable but draining
- Low ROAS, high volatility (bottom-right) — sunset candidates

A secondary visualization: stacked bar by month showing channel-mix evolution over the year. Where has the user's channel mix actually drifted?

### Step 4 — Model scenarios
Generate 2-3 budget mix scenarios:
- **"Hold" scenario** — same total budget, current channel mix, projected outcomes
- **"Growth" scenario** — total budget +X%, weighted toward growing channels
- **"Efficiency" scenario** — same or smaller total budget, weighted toward stable + growing, sunset the declining ones

For each scenario, project total conversions, total revenue, blended ROAS, and total CAC if CRM data is available.

### Step 5 — Build the annual plan doc
Use `assets/annual_plan_doc.md`. The doc structure: market context (1 page), current-state recap (1 page), channel portfolio analysis (2 pages including the quadrant chart), scenario comparison (2 pages), recommendation with rationale (1 page), risk factors and assumptions (1 page).

Use the `docx` skill to produce the actual Word doc — annual plans get printed, marked up, and circulated. For the slides version, use the `pptx` skill in parallel.

### Common variations
- **Add a "new channels" scenario** — combine with Workflow 5's analytical output for proposing new platforms.
- **Pipeline contribution overlay** — if HubSpot or Salesforce is connected, overlay pipeline-influenced revenue on top of direct-attribution revenue. Often changes the rank order.
- **Multi-year comparison** — if 24+ months of data is available, show 2-year-over-2-year trends rather than month-by-month.

---

### New channel investment case
**When to use:** the user is considering launching on a new platform (Reddit Ads, Pinterest, TikTok, Spotify Ads, retail media platforms, a new DSP) and needs to justify the investment. Often paired with annual planning.

**Platforms typically involved:** current ad platforms (for the saturation analysis) + web search (for benchmark data on the proposed new channel).

### Step 1 — Audit current channel saturation
Audience saturation is the strongest signal that a new channel is worth trying. Pull the last 6 months of CPM, CPC, and frequency trends per current channel.

Use `scripts/channel_investment_case.py` to identify channels showing saturation signals:
- CPM trending up consistently
- Frequency climbing
- CTR trending down at flat or rising CPCs

These are the channels where incremental dollars are getting expensive. If they're showing saturation, a new channel is more likely to outperform incremental spend on the current ones.

### Step 2 — Estimate audience overlap
GA4 doesn't expose cross-channel audience overlap through its reporting API. As a proxy, pull GA4 `newUsers` vs total users by `firstUserDefaultChannelGrouping`: channels still bringing a high share of new users are reaching fresh audiences; a falling share suggests current channels are recycling the same people.

### Step 3 — Research the new channel
Use web search to find recent benchmarks for the proposed new channel: typical CPM, CPC, conversion rate, audience profile. Note the date and source of every benchmark — these change quickly.

If similar companies in the user's category have published case studies on the proposed channel, surface those. Note bias: published case studies are usually success stories.

### Step 4 — Visualize the case
The question the chart needs to answer: **why does this new channel make sense given our current portfolio?**

A useful primary visualization is a stacked or grouped bar chart showing per-channel saturation signals (CPM trend, frequency, CTR trend) over the past 6 months. Channels showing red across multiple signals are saturation cases.

A secondary visualization: a comparison table or bar chart showing the proposed new channel's expected CPM/CPC/conversion rate (from research) against the user's blended current numbers, with the assumption explicitly labeled.

### Step 5 — Build the business case
Use `assets/new_channel_business_case.md`. The case includes:
- The saturation evidence (chart + summary)
- The expected economics on the new channel (with assumptions)
- A proposed test budget and timeline (typically $X for Y weeks)
- Success criteria — what would justify continuing? What would mean we pull out?
- The opportunity cost — what current channel spend is being deprioritized to fund the test?

### Common variations
- **For retail media specifically** — Walmart Connect, Amazon Ads, Criteo Retail Media. These have different economics (much higher CPC but lower-funnel intent). Frame as "incremental ROAS during high-purchase-intent moments" rather than head-to-head with social.
- **For audio (Spotify Ads)** — different audience and creative requirements. Surface the production cost as well as the media cost.
- **For B2B with LinkedIn Lead Gen Forms** — if the user is already on LinkedIn Ads, this is about expanding format usage not adding a channel.

---

### Marketing efficiency vs goals tracking
**When to use:** the user has committed targets — CAC, payback period, MER, or other goals — and needs to track actual performance against them, surface variance, and explain misses.

**Platforms typically involved:** ad platforms + CRM (HubSpot, Salesforce) + ecommerce or payments connector for revenue context.

### Step 1 — Confirm the targets
Ask the user (with a multiple-choice question tool if available): what are the committed targets, and for what time period? Common ones:
- **CAC** — cost per acquired customer
- **MER** (Marketing Efficiency Ratio) — total revenue / total marketing spend
- **Payback period** — months until LTV exceeds CAC
- **Blended ROAS** — sum revenue / sum spend across all marketing
- **CPL** — cost per lead (for B2B)

### Step 2 — Pull and compute actuals
Pull spend from ad platforms, revenue from ecommerce/CRM. Compute the same metrics the user committed to.

Use `scripts/efficiency_tracker.py`. The script accepts the target values and computes:
- Actual value
- Variance from target (absolute and percent)
- Trend direction (improving, holding, declining)

### Step 3 — Visualize the gap
The question the chart needs to answer: **are we on target, and if not, where's the gap?**

A horizontal bar chart with one bar per metric, where each bar shows actual value with the target as a reference line, works well. Color the gap red when over target on a "lower is better" metric (CAC), green when under it. The user sees at a glance which metrics are meeting targets.

A secondary visualization: a small line chart per metric showing the trailing 90-day trend, with the target as a horizontal reference line. Surfaces whether the gap is widening, holding, or closing.

### Step 4 — Diagnose
For each metric that's missing target, surface 2-3 likely causes from the underlying data:
- CAC too high → which channels are driving the average up?
- Payback period too long → has AOV dropped, or has new-customer CAC risen?
- MER too low → has total revenue declined, or has total spend risen disproportionately?

Use `assets/efficiency_dashboard_doc.md` for the structured output.

### Step 5 — Output
Default delivery: chart + diagnosis inline as markdown. If the user is sharing this with their leadership team monthly or quarterly, use the `docx` skill.

### Common variations
- **Cohort-level CAC** — if the user has month-of-acquisition cohorts, compute CAC per cohort rather than blended. Surfaces whether recent cohorts are getting more expensive to acquire.
- **Channel-attributed CAC** — split blended CAC into per-channel CAC using a defined attribution model. State which model.
- **CAC payback by cohort** — for SaaS or subscription businesses, this is the real question. Cohort revenue over time vs. cohort acquisition cost.

---

### CAC and LTV unit economics
**When to use:** the user wants the finance-team view of marketing — what does it cost to acquire a customer, what are they worth over their lifetime, what's the LTV:CAC ratio. The deliverable typically goes to a CFO, board, or investor.

**Platforms typically involved:** ad platforms (for CAC numerator) + CRM (HubSpot, Salesforce, Pipedrive) and/or ecommerce/payments (Shopify, Stripe, Recharge) for LTV. This workflow is significantly more valuable when both sides are connected.

### Step 1 — Confirm what counts as a customer
Ask the user (with a multiple-choice question tool if available): which event defines "acquired customer"?
- First purchase (ecommerce default)
- Closed-won opportunity (B2B default)
- Subscription start (SaaS default)
- Form fill + qualification (lead-gen heavy businesses)

Also confirm the LTV horizon: 12-month LTV is common, 24-month gives a fuller picture for SaaS.

### Step 2 — Pull CAC numerator (marketing spend)
> Pull total marketing spend for the last [12-24] months across [all connected ad platforms].

Optionally include other marketing costs (tools, content production) if the user wants a fully-loaded CAC — but state that clearly in the output.

### Step 3 — Pull CAC denominator and LTV (customers and revenue)
Pull customer acquisition counts and revenue from the CRM or ecommerce backend over the same period.

For ecommerce: cohort customers by first-purchase month, sum their cumulative revenue over the horizon.

For B2B: cohort customers by closed-won month, sum closed-won revenue + expansion revenue over the horizon.

### Step 4 — Compute unit economics
Use `scripts/unit_economics.py`. Outputs:
- Blended CAC (total marketing spend / total new customers in the period)
- Per-channel CAC: run the script once per channel's cohort data
- LTV (cumulative revenue per acquired customer)
- LTV:CAC ratio (3:1 is the SaaS rule of thumb; 4:1+ is healthier for venture-funded businesses)
- Payback period (months until LTV exceeds CAC)

### Step 5 — Visualize the unit economics
Two charts that work together:
- **Cohort LTV curve**: x-axis = months since acquisition, y-axis = cumulative revenue per customer. One line per acquisition cohort. The shape of these curves is the most important visualization in unit economics — flat curves mean low retention, steep curves mean strong retention.
- **CAC vs LTV bars by channel**: paired bars per channel showing CAC and LTV. The gap between the pairs is the channel-level contribution margin.

### Step 6 — Build the finance-ready doc
Use the `docx` skill. The doc should be one page if possible, with:
- Headline LTV:CAC ratio and trend (improving/stable/deteriorating)
- The cohort LTV chart
- Per-channel unit economics if available
- Assumptions: what's included in "marketing spend", what's the LTV horizon, what attribution is used for per-channel CAC
- A note that this is a snapshot of past cohorts; forward projections require additional assumptions

### Common variations
- **Subscription business** — pull monthly recurring revenue (MRR) cohorts rather than total revenue. The chart becomes MRR retention curves, which is the standard SaaS view.
- **Heavy seasonality** — overlay multiple acquisition-quarter cohorts to see whether different acquisition periods have systematically different LTV.
- **By acquisition channel** — when ad-platform → CRM customer ID linkage is available, compute per-channel LTV. Often shows that the cheapest CAC channels have the worst retention.

---

## Ad hoc analysis

For one-off leadership questions outside the seven workflows above ("what's the concentration of spend across our top 3 vs the rest?", "is there a launch-month effect on new campaigns?", "how did our CPA trend during the last price change?"), write fresh analysis code in the chat. The "Choosing the right visualization" framework in the SKILL.md tells you which chart family fits which type of question.

---

## A note on the leadership audience

Three patterns that consistently work when the audience is a marketing leader:

- **Headline → support → recommendation.** Open every deliverable with one sentence that summarizes the whole thing. Then the data. Then what to do about it.
- **Specific dollar amounts beat percentages.** "Shift $40k to LinkedIn next month" lands harder than "shift 20% of the budget." Use percentages in headlines, dollars in recommendations.
- **Name the trade-off.** Every recommendation cuts something. If you don't name it, the audience will, and the conversation will pivot to that instead of the recommendation. Get there first.

## Background

Chart choices, principles and gotchas for this area: [references/marketing-leader-guide.md](references/marketing-leader-guide.md).

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
