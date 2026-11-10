# Ecommerce ROAS analysis — workflow details

Chart choices and common variations for each workflow in SKILL.md. Read the section for the workflow you are running when you reach its visualization step, or when the user asks for a variant of the workflow.

## Cross-platform ROAS and purchase behavior

### Chart choices

Two questions worth surfacing, often as two charts:

**"Is ROAS trending up, down, or stable?"** A line chart of daily blended ROAS over the 30-day window, with a horizontal reference line at the period average. The chart makes drift visible — ROAS that's volatile day-over-day tells a different story than ROAS drifting in one direction.

**"Which days of the week perform best?"** This is the prime case for a heatmap: rows = day of week (Monday-Sunday), columns = a single column if data is daily-only, or hour columns if hourly data is available. Cell color = ROAS or conversion rate, with a diverging red-to-green scale centered on the period average. Above-average days appear green, below-average appear red, intensity shows magnitude.

If a heatmap is overkill for the user's data shape, a sorted horizontal bar chart of average ROAS by day of week works as the fallback.

### Common variations

- **Add TikTok, Pinterest** — same workflow, expand the platform list. Watch for platforms with low spend producing noisy daily numbers.
- **Compare to prior 30-day period** — pull a second window and overlay both lines on the trend chart.
- **Break down by product category** — needs product-level data from Shopping/catalog feeds. Available if the user runs Shopping campaigns; not available if they run purely image/video.

## Promotional campaign anomaly detection

### Chart choices

A line chart showing the trailing 7 days of cost-per-purchase per anomalous ad set works well — most days sit in a horizontal band, then yesterday's value spikes upward. The spike day gets highlighted in amber. A reference line at the baseline average makes the deviation visible.

When multiple ad sets are anomalous, a sorted horizontal bar chart of yesterday's CPP-to-baseline ratio surfaces which are the worst — the user fixes the biggest bleeders first.

For non-anomalous ad sets, no chart is needed — just the headline.

### Common variations

- **Baseline isn't 6 days, it's same-promo-week-last-year** — the script only supports a trailing baseline (`--baseline-days`); compute ratios against last year's promo week ad hoc.
- **Monitor ROAS, not cost per purchase** — same workflow, swap the metric. Pass `--metric roas`.
- **Hourly granularity, not daily** — supported on Facebook and Google but noise floor is high. Use only for high-spend ($50k+/day) campaigns.

## Product / SKU-level performance

### Chart choices

A scatter plot works well: x-axis = total revenue, y-axis = ad ROAS for that SKU. Dots colored by margin tier (high/medium/low margin), sized by units sold. The user sees the high-revenue, high-ROAS, high-margin winners immediately.

A secondary view: top 20 SKUs by ad-attributed revenue, with bars colored by margin tier. Surfaces concentration — often 5-10 SKUs drive 80% of ad revenue.

### Common variations

- **Add inventory levels** — if an inventory feed is available, surface SKUs with low stock to avoid promoting out-of-stock products.
- **By collection or category** — instead of SKU-by-SKU, group into categories for higher-level decisions.
- **Seasonal SKUs** — for seasonal products, surface the year-over-year same-period comparison.

## New vs returning customer ROAS by channel

### Chart choices

A stacked bar chart per channel works well: each bar shows the channel's total revenue, split into new and returning customer revenue. Surfaces channels that are mostly retargeting vs mostly acquisition.

A secondary view: paired bars per channel showing new-customer-CAC and returning-customer-CAC side by side. Some channels acquire new customers at $X but the channel's apparent ROAS is inflated by returning-customer revenue.

### Common variations

- **Compare to organic** — many ecommerce businesses get more new customers via organic than paid. Surface organic for context.
- **Subscription / repeat purchase business** — the workflow becomes "first-purchase ROAS" vs "subsequent-purchase ROAS" where channels driving subsequent purchases are particularly valuable.
- **By acquisition cohort** — track new customers acquired this month by their first-purchase channel; see how they behave over the next 90 days.

## AOV and basket composition

### Chart choices

A box plot of AOV by channel surfaces both central tendency and distribution. A complementary histogram of order values with vertical reference lines at common free-shipping thresholds shows the upsell opportunity.

A bar chart of items-per-order distribution (1, 2, 3, 4+ items) by channel shows which channels drive single-item vs basket-building purchases.

### Common variations

- **Pre/post threshold change** — if the user has changed their free-shipping threshold recently, compare AOV distributions pre/post.
- **Promotional vs non-promotional periods** — AOV typically rises during promos (people buy more to hit discount thresholds). Surface the contrast.
- **First-time-buyer AOV vs repeat-buyer AOV** — repeat buyers usually have higher AOV. The size of the gap tells you how much retention is worth.

## Subscription / retention cohort analysis

### Chart choices

Two complementary visualizations:
- **Retention curves**: line chart with one line per cohort. X-axis = months since acquisition, Y-axis = % of cohort still active or % of cohort generating revenue. Steeper curves = worse retention; flatter curves = better retention. Overlapping curves means recent cohorts behave like older ones; diverging curves (with newer cohorts lower) is a warning sign.
- **Cumulative cohort revenue per customer**: line chart with one line per cohort showing $ revenue per customer over time. Reach to CAC level marks the payback moment.

### Common variations

- **By acquisition channel** — different channels often produce cohorts with very different retention. The cheapest CAC channel often has the worst retention.
- **By acquisition campaign or promo** — heavy promo cohorts (50% off first month) typically have worse retention than full-price cohorts. Worth surfacing.
- **By product mix on first purchase** — for ecommerce, the first product purchased often predicts retention. Subscription-product-first vs one-time-product-first cohorts behave very differently.

## Pre-promo planning model

### Chart choices

A bar chart comparing prior year actuals vs proposed plan for each metric (spend, purchases, revenue, AOV, ROAS) makes the proposal scannable.

A second chart projects daily spend over the promo period — useful for media buyers planning the pacing.

### Common variations

- **First-time-running-this-promo** — no prior data. Use industry benchmarks from web search; surface the higher uncertainty explicitly.
- **Different mix this year** — if the channel mix differs from prior year (e.g. adding TikTok), the prior-year model needs adjustment. Surface what's being assumed.
- **Multi-week promo** — model per-week pacing if the promo runs more than 3-4 days. Customer behavior often differs early-promo vs late-promo.
