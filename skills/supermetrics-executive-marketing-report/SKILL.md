---
name: supermetrics-executive-marketing-report
description: >-
  Produces leadership-level marketing reporting from live data: 90-day cross-channel executive
  overviews, efficiency against committed targets (CAC, MER, payback, blended ROAS), CAC and LTV
  unit economics with cohort curves, annual budget planning with scenarios, and business cases
  for a new ad channel, delivered as decks, memos or CMO emails. Use when the user asks "prepare
  our quarterly marketing review for the board", "are we hitting our CAC target", "what is our
  LTV to CAC ratio", or "build next year's marketing budget plan". Don't use for building a
  live, shareable Studio dashboard (use supermetrics-live-dashboards) or weekly campaign-level
  reviews (use supermetrics-campaign-performance-review).
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
The first chart the user sees should answer: **what's the story this quarter?** Build a high-level summary visualization before drilling into channel-level detail. Pick one headline chart, not three.

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "The 90-day executive overview".

### Step 3 — Channel-level detail
For each channel with meaningful spend, surface the key metrics: spend, revenue, conversions, ROAS, CPA, share of total spend, share of total revenue.

When the comparison is "which channel is pulling its weight?", a side-by-side chart of spend-share vs revenue-share per channel makes mismatches obvious immediately — channels well above the diagonal are over-delivering, channels below it are under-delivering.

### Step 4 — Recommendations
Use `scripts/exec_summary_builder.py` to produce a structured outline of the deck content. The script handles the slide skeleton; the user's analysis fills in the callouts and recommendations.

Build from `assets/exec_deck_outline.md`. Recommendations should be specific: name a channel, name a dollar amount or percent shift, name the expected impact.

### Step 5 — Build the deck
If the client can create files, produce the actual slide deck (`.pptx`). Keep each slide to one message with a headline that states the takeaway; slides have layout conventions that don't apply to documents.

Embed the visualizations from steps 2 and 3 inside the deck. Don't put long text on slides — bullets and callouts only. The deck is a talking aid.

### Step 6 — Companion deliverables (only if asked)
For a quarterly review the team will reopen, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants a handout to accompany the deck, create a Word document file to produce a Google Doc–style summary. If they want a CMO email, build from `assets/cmo_email.md` and deliver inline as markdown (so they can edit before sending). If Gmail is connected, offer to save as a draft.

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

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Annual planning support".

### Step 4 — Model scenarios
Generate 2-3 budget mix scenarios:
- **"Hold" scenario** — same total budget, current channel mix, projected outcomes
- **"Growth" scenario** — total budget +X%, weighted toward growing channels
- **"Efficiency" scenario** — same or smaller total budget, weighted toward stable + growing, sunset the declining ones

For each scenario, project total conversions, total revenue, blended ROAS, and total CAC if CRM data is available.

### Step 5 — Build the annual plan doc
Use `assets/annual_plan_doc.md`. The doc structure: market context (1 page), current-state recap (1 page), channel portfolio analysis (2 pages including the quadrant chart), scenario comparison (2 pages), recommendation with rationale (1 page), risk factors and assumptions (1 page).

If the client can create files, produce the actual Word document — annual plans get printed, marked up, and circulated. For the slides version, create a slide deck file in parallel.

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

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "New channel investment case".

### Step 5 — Build the business case
Use `assets/new_channel_business_case.md`. The case includes:
- The saturation evidence (chart + summary)
- The expected economics on the new channel (with assumptions)
- A proposed test budget and timeline (typically $X for Y weeks)
- Success criteria — what would justify continuing? What would mean we pull out?
- The opportunity cost — what current channel spend is being deprioritized to fund the test?

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

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Marketing efficiency vs goals tracking".

### Step 4 — Diagnose
For each metric that's missing target, surface 2-3 likely causes from the underlying data:
- CAC too high → which channels are driving the average up?
- Payback period too long → has AOV dropped, or has new-customer CAC risen?
- MER too low → has total revenue declined, or has total spend risen disproportionately?

Use `assets/efficiency_dashboard_doc.md` for the structured output.

### Step 5 — Output
Default delivery: chart + diagnosis inline as markdown. If the user is sharing this with their leadership team monthly or quarterly, create a Word document file.

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
Answer: **how fast does each cohort earn back its acquisition cost, and which channels have the widest LTV-to-CAC gap?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "CAC and LTV unit economics".

### Step 6 — Build the finance-ready doc
Produce it as a Word document if the client can create files. The doc should be one page if possible, with:
- Headline LTV:CAC ratio and trend (improving/stable/deteriorating)
- The cohort LTV chart
- Per-channel unit economics if available
- Assumptions: what's included in "marketing spend", what's the LTV horizon, what attribution is used for per-channel CAC
- A note that this is a snapshot of past cohorts; forward projections require additional assumptions

---

## Ad hoc analysis

For one-off leadership questions outside the five workflows above ("what's the concentration of spend across our top 3 vs the rest?", "is there a launch-month effect on new campaigns?", "how did our CPA trend during the last price change?"), write fresh analysis code in the chat. The "Choosing the right visualization" section of [references/marketing-leader-guide.md](references/marketing-leader-guide.md) tells you which chart family fits which type of question.

---

## A note on the leadership audience

Three patterns that consistently work when the audience is a marketing leader:

- **Headline → support → recommendation.** Open every deliverable with one sentence that summarizes the whole thing. Then the data. Then what to do about it.
- **Specific dollar amounts beat percentages.** "Shift $40k to LinkedIn next month" lands harder than "shift 20% of the budget." Use percentages in headlines, dollars in recommendations.
- **Name the trade-off.** Every recommendation cuts something. If you don't name it, the audience will, and the conversation will pivot to that instead of the recommendation. Get there first.

## Background

Chart choices, principles and gotchas for this area: [references/marketing-leader-guide.md](references/marketing-leader-guide.md).

## Before you analyze

- Load saved targets, budgets and known context with `get_business_context` and judge results against them, not generic benchmarks.
- Query correctly: follow the `supermetrics-marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `supermetrics-campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
