---
name: supermetrics-ecommerce-roas-analysis
description: >-
  Analyzes ecommerce paid acquisition from live ad and store data: blended and per-platform ROAS
  with day-of-week patterns, promo cost-per-purchase anomaly alerts, SKU-level ad performance
  and margin, new vs returning customer ROAS, AOV and free-shipping thresholds, subscription
  retention cohorts and pre-promo (BFCM) planning. Covers Shopify, BigCommerce, WooCommerce,
  Recharge and Stripe alongside Google Ads, Meta Ads and TikTok Ads. Use when the user asks
  "what's our blended ROAS this month", "is our Black Friday cost per purchase spiking", "which
  products should we push in ads", or "are ads acquiring new customers or returning ones". Don't
  use for channel budget pacing (use supermetrics-budget-pacing-allocation) or leadership CAC
  and LTV reports (use supermetrics-executive-marketing-report).
---

# Ecommerce roas analysis

## Workflows

### Cross-platform ROAS and purchase behavior
**When to use:** the user is doing a periodic (usually monthly) review of paid acquisition efficiency across their main ecommerce platforms, and wants to surface day-of-week patterns to inform bid strategy.

**Platforms typically involved:** Google Ads and Facebook Ads at minimum. Sometimes TikTok Ads, Pinterest Ads, Snapchat Ads, Amazon Ads.

### Step 1 — Pull the data
> Pull the last 30 days of daily ad spend and purchase conversion value from [Google Ads, Facebook Ads, etc.].

Daily granularity matters — day-of-week analysis requires it. Make sure data has conversion value (revenue), not just count. If conversion value isn't populated, this workflow can't run as designed; surface that and offer the count-based version instead.

### Step 2 — Compute blended ROAS
Use `scripts/blended_roas.py`. The script computes blended ROAS daily, weekly, and by day-of-week. Mention the double-count caveat once when introducing the number, not repeatedly.

### Step 3 — Visualize the trend and patterns
Answer two questions, usually as two charts: **is ROAS trending up, down or stable?** and **which days of the week perform best?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Cross-platform ROAS and purchase behavior".

### Step 4 — Budget weighting recommendation
> Build a strategic plan for adjusting weekly budget allocation to weight spending more heavily toward the days demonstrating the strongest purchase intent.

Use `assets/dow_budget_strategy.md`. The recommendation should propose a weighting (e.g. Tuesday +20%, Friday -15%) and explicitly flag what would invalidate the strategy — usually a promo period scrambling normal patterns.

### Step 5 — Output
Default delivery: charts in chat plus a markdown recommendation. For a ROAS view the team will reopen, offer once to publish it as a live Studio dashboard (with `manage_dashboards`). If the user wants a Word doc to share with the team, create a Word document file.

---

### Promotional campaign anomaly detection
**When to use:** the user is running a high-stakes promo (BFCM, holiday sale, flash sale) and wants to catch cost-per-purchase anomalies before they burn the day's budget.

**Platforms typically involved:** Facebook Ads most often (BFCM spend concentrates there for many DTC brands). Google Ads where applicable.

### Step 1 — Pull yesterday's promo data
> Pull yesterday's data for the [promo campaign filter — e.g. campaigns containing "BFCM" or "Black Friday"] on Facebook Ads. Include cost, impressions, and total purchases per ad set.

Use the user's actual filter convention. Confirm if unclear. Ad-set granularity matters — a campaign-level 3x spike might be driven by one bad ad set, not the whole campaign.

### Step 2 — Pull the baseline
Get the prior 6 days of data for the same campaigns. The script computes the rolling-average baseline.

### Step 3 — Flag the anomaly
Use `scripts/anomaly_detector.py`. Default threshold: yesterday's cost-per-purchase >3x the trailing 6-day average. The script identifies which ad sets are responsible and flags candidate causes (tracking failure vs real performance issue).

If no anomaly is detected, surface that clearly. "No anomaly, performance within expected range" is a valid and useful answer.

### Step 4 — Visualize the spike (if one exists)
The question the chart needs to answer: **which ad sets are anomalous, by how much, and what's the likely cause?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Promotional campaign anomaly detection".

### Step 5 — Verify before alerting
Before drafting any alert email: verify the spike is real, not a tracking issue. The most common causes of an apparent 3x spike are pixel failures, renamed events, or deduplication issues — not real performance failures. The script's candidate-cause column points at likely explanations; investigation is on the user.

### Step 6 — Draft the urgent alert
Use `assets/media_buyer_alert_email.md`. The email needs to be short, specific (named ad sets), and recommend a concrete action.

Default delivery: markdown inline so the user reviews before sending. If Gmail is connected, offer to save as a draft. If the user prefers Slack and a Slack connector is available, post the alert to the relevant channel instead — often faster during a promo when the media buying team is fielding many messages per hour.

---

### Product / SKU-level performance
**When to use:** the user wants to know which specific products are driving paid-acquisition revenue, not just which campaigns. Used to inform creative direction, inventory planning, and bid strategy.

**Platforms typically involved:** an ecommerce backend (Shopify, BigCommerce, WooCommerce, Adobe Commerce, Squarespace, Wix, PrestaShop, Centra, Ecwid, Amazon Seller Central, TikTok Shop) + ad platforms with product-level reporting (Google Shopping, Meta Catalog ads, TikTok Shop ads).

### Step 1 — Pull product-level sales
> Pull the last 30-60 days of product-level sales from [Shopify / BigCommerce / WooCommerce]: SKU, product name, units sold, revenue, gross margin if available.

### Step 2 — Pull ad-driven attribution
> Pull product-level performance from [Google Shopping / Meta product catalog / TikTok Shop ads]: SKU, impressions, clicks, spend, attributed purchases, attributed revenue.

### Step 3 — Join and rank
Use `scripts/sku_performance.py`. The script:
- Joins ad-driven attribution with total sales
- Computes the % of each SKU's revenue that came from paid ads
- Surfaces high-margin SKUs underspending on ads (opportunity)
- Surfaces low-margin SKUs overspending on ads (cut candidates)
- Flags SKUs with high return rates if return data is available

### Step 4 — Visualize the SKU portfolio
The question the chart needs to answer: **which products deserve more ad investment, which deserve less?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Product / SKU-level performance".

### Step 5 — Build the report
Use `assets/sku_performance_report.md`. The doc should recommend specific products for:
- **Scale up** — high ROAS, high margin, inventory available
- **Hold** — performing as expected
- **Cut from ads** — low ROAS or low margin, ad spend not earning
- **Add to ads** — strong organic sales, not yet promoted, high margin

---

### New vs returning customer ROAS by channel
**When to use:** the user wants to know whether their ad spend is acquiring new customers or just re-converting existing ones. Different channels often have very different splits.

**Platforms typically involved:** ad platforms + ecommerce backend with customer-type segmentation (Shopify, BigCommerce, etc.) + GA4 if available (for the customer-type dimension on conversions).

### Step 1 — Pull channel-level spend
> Pull the last 30-60 days of spend by channel from [ad platforms].

### Step 2 — Pull purchases segmented by customer type
> Pull purchases segmented into "new customer" (first-time buyer) and "returning customer" (repeat) from [Shopify / GA4]. Break out by channel.

Prefer the store's own flag (Shopify `order_is_returning_customer`, with `order_utm_source` for channel) — it is purchase-based. GA4's `newVsReturning` counts prior sessions, not prior purchases, so it isn't a buyer split; GA4's first-time purchaser metrics are closer.

### Step 3 — Compute split ROAS
Use `scripts/customer_type_roas.py`. Outputs per channel:
- Total ROAS
- New-customer ROAS (revenue from new buyers / spend)
- Returning-customer ROAS
- % of revenue from new customers
- Cost per new customer (CAC)

### Step 4 — Visualize the split
The question the chart needs to answer: **which channels acquire new customers, and which mostly serve repeat buyers?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "New vs returning customer ROAS by channel".

### Step 5 — Build the brief
Use `assets/customer_acquisition_brief.md`. The interesting finding usually centers on which channels are real acquisition vs which are retargeting in disguise.

---

### AOV and basket composition
**When to use:** the user wants to understand average order value (AOV) and items-per-order patterns, often as a precursor to upsell, cross-sell, or free-shipping-threshold decisions.

**Platforms typically involved:** ecommerce backend (Shopify, BigCommerce, WooCommerce, etc.) + ad platforms for channel-level breakdown.

### Step 1 — Pull order-level data
> Pull the last 30-90 days of order-level data from [ecommerce backend]: order value, item count, products, channel attribution if available.

If channel attribution isn't on the order data, join with ad-platform purchase data by date/customer.

### Step 2 — Compute AOV and basket patterns
Use `scripts/aov_analyzer.py`. Outputs:
- AOV by channel
- Items per order distribution
- AOV percentiles (P25, P50, P75, P90) to understand the distribution shape
- Free-shipping-threshold proximity (% of orders just under common thresholds like $50, $75, $100)

### Step 3 — Visualize the patterns
The question the chart needs to answer: **what does the basket look like, and where's the upsell opportunity?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "AOV and basket composition".

### Step 4 — Build the summary
Use `assets/aov_summary.md`. The most-useful section is usually the free-shipping-threshold analysis — if a meaningful % of orders are just under the threshold, raising or lowering it has predictable effects.

---

### Subscription / retention cohort analysis
**When to use:** the user runs a subscription business (Recharge on Shopify, native subscription products) or a repeat-purchase ecommerce business and wants to track retention by acquisition cohort.

**Platforms typically involved:** subscription platform (Recharge), payments (Stripe), or ecommerce backend with subscription/repeat-purchase data + ad platforms for cohort acquisition cost.

### Step 1 — Pull cohort data
> Pull customers grouped by acquisition month for the last 12 months. For each cohort, pull monthly revenue or active subscriber count for each month since acquisition.

### Step 2 — Pull acquisition costs
> Pull marketing spend per month over the same period. If channel-level CAC is available, pull that too.

### Step 3 — Compute retention metrics
Use `scripts/subscription_cohorts.py`. Outputs:
- Cohort retention curve (% of cohort still active by month-since-acquisition)
- Cohort cumulative revenue per customer
- Time-to-payback (months for cumulative revenue per customer to exceed CAC)
- Cohort LTV at 3/6/12/24 month horizons

### Step 4 — Visualize the cohort behavior
The question the chart needs to answer: **how does customer behavior change with cohort age, and are recent cohorts behaving like older ones?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Subscription / retention cohort analysis".

### Step 5 — Build the report
Use `assets/retention_cohort_report.md`. The audience is typically the marketing leader and possibly the CFO — retention numbers feed unit economics decisions.

---

### Pre-promo planning model
**When to use:** the user is planning a high-stakes promotional period (BFCM, summer sale, etc.) and needs to set budget, AOV, and ROAS targets based on historical performance.

**Platforms typically involved:** ad platforms + ecommerce backend, both showing prior promo periods.

### Step 1 — Pull prior promo data
> Pull spend, purchases, revenue, AOV, and ROAS from the equivalent promo period last year (and the year before if available) across all relevant channels.

Use the actual promo dates, not "last November" — promos have specific date windows that matter.

### Step 2 — Pull baseline data
> Pull the same metrics from the trailing 30 days before the prior promo started. Used to compute the promo lift over baseline.

### Step 3 — Model the upcoming promo
Use `scripts/promo_planner.py`. Inputs:
- Last year's promo numbers (auto-derived from the pulled data)
- Year-over-year growth assumption (user provides)
- Planned budget for the upcoming promo

Outputs:
- Projected purchases, revenue, AOV, ROAS for the upcoming promo
- Per-channel projected spend and outcomes
- Stretch and conservative scenarios

### Step 4 — Visualize the plan
Make the proposal scannable: **how does the plan compare with last year, and how will daily spend pace?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Pre-promo planning model".

### Step 5 — Build the plan
Use `assets/promo_plan_doc.md`. The plan must include:
- Targets per metric (with stretch and conservative bands)
- Per-channel allocation
- Daily pacing
- Anomaly thresholds (when to alert the team mid-promo)
- Post-promo measurement plan

---

## Ad hoc analysis

For one-off questions outside these seven workflows ("what's the AOV distribution across our top SKUs in this campaign?", "is there an LTV difference between Facebook and Google traffic?", "did this discount code drive incremental purchases or cannibalize existing demand?"), write fresh analysis code. The "Choosing the right visualization" section of [references/ecommerce-marketer-guide.md](references/ecommerce-marketer-guide.md) tells you which chart family fits.

---

## A note on promo-period analysis

Normal-period and promo-period analysis behave differently:

- **Conversion rates spike during promos** because intent is high. Day-of-week patterns from non-promo periods don't predict promo behavior.
- **CPMs spike during promos** because everyone is bidding harder. Cost-per-purchase increases during BFCM aren't always efficiency loss — it might be competitive pressure.
- **Pixels and tracking break more during promos** because traffic volume is higher and edge cases get exposed. Before alerting on an anomaly, verify tracking is healthy.

When the user is doing post-promo analysis, frame as "vs. last year's same period" rather than "vs. last month" — same-period comparison removes the seasonal confound.

## Background

Chart choices, principles and gotchas for this area: [references/ecommerce-marketer-guide.md](references/ecommerce-marketer-guide.md).

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
