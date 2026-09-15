---
name: google-analytics-4
description: Query Google Analytics 4 through Supermetrics. Use for website and app traffic, sessions, users, engagement rate, bounce rate, landing pages, channel and source/medium attribution, conversions and key events, ecommerce revenue and purchases, funnels, audience and geography breakdowns, and cross-channel comparisons against ad platform data.
---

# Google Analytics 4 with Supermetrics

`GAWA` · accounts are **properties** · date range always required.

Web and app analytics: what happened on the site. No ad spend of its own beyond imported
`advertiserAdCost`, and **read-only** — Supermetrics cannot write to GA4.

## Rules

- **Never invent.** Every number, page path, channel, campaign and date must come from a tool
  response in this conversation. No estimates, no illustrative examples, no typical GA4 patterns.
  Empty result → say it was empty. Can't retrieve it → say what failed.
- **Never guess a field ID.** 834 fields here, many property-specific custom key events. A wrong
  guess is not safely rejected: a loose name like `pageviews` or `users` can alias to a metric with a
  different definition. IDs come from `field_discovery`; confirm with `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="GAWA")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="GAWA")` — properties are often ambiguously named; confirm which one
   the user means rather than taking the first match.
3. `manage_business_context(action="get", ds_id="GAWA", account_id=…)` — which key events count,
   preferred channel grouping, internal traffic exclusions. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="GAWA", filter="…")` — always filter. This property's custom key events
   appear as `c_keyevents_*`, `c_sessionkeyeventrate_*`, `c_userkeyeventrate_*` — discover, never assume.
5. `data_query(…)` — dimensions before metrics.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Audience / engagement** `sessions` `totalUsers` `activeUsers` `newUsers` `engagedSessions`
`engagementRate` `bounceRate` `averageSessionDuration` `userEngagementDuration` `screenPageViews`
`screenPageViewsPerSession` `sessionsPerUser` `eventCount` `eventsPerSession`
**Conversions** `conversions` `conversionsFloat` `sessionConversionRate` `userConversionRate`
(per-event key events are property-specific `c_keyevents_<event>` fields)
**Ecommerce** `purchaseRevenue` `totalRevenue` `transactions` `ecommercePurchases`
`averagePurchaseRevenue` `purchaserConversionRate` `firstTimePurchasers` `addToCarts` `checkouts`
`cartToViewRate` `purchaseToViewRate` `itemRevenue` `refundAmount` `ARPU` `ARPPU`
**Imported paid media** `advertiserAdCost` `advertiserAdClicks` `advertiserAdImpressions`
`advertiserAdCostPerClick` `advertiserAdCostPerConversion` `returnOnAdSpend`
**Organic search** `organicGoogleSearchClicks` `organicGoogleSearchImpressions`
`organicGoogleSearchClickThroughRate` `organicGoogleSearchAveragePosition`
**Dimensions** `date` `currencyCode` `sessionDefaultChannelGrouping` `sessionSource` `sessionMedium`
`sessionSourceMedium` `sessionCampaignName` `firstUserDefaultChannelGrouping` `landingPage`
`landingPagePlusQueryString` `pagePath` `pageTitle` `deviceCategory` `browser` `operatingSystem`
`country` `city` `newVsReturning` `eventName` `isConversionEvent` `audienceName` `itemName`
`transactionId` `sessionGoogleAdsCampaignName` `sessionGoogleAdsKeyword` `hostName` `contentGroup`

## Good to know

- **Max 9 dimensions and 10 metrics per query** (verified — a 10-dimension query is rejected). On a
  multi-day range `date` uses one of the 9 slots; on a single-day range it doesn't consume a slot and
  the Date column still comes back carrying that one date.
- **Not every dimension pairs with every metric.** GA4 rejects incompatible pairs. Reduce to one
  dimension and one metric, confirm it runs, add fields back.
- **Non-aggregatable**: `activeUsers`, `active7DayUsers`, `active28DayUsers` and the rates. Users
  never add up — the same person appears in several rows. Re-query at the grouping you want.
- **Thresholding hides whole rows, not one number.** Where active or total users are thresholded to
  zero, every other metric on that row returns nothing even when it has data. A blank is not a zero.
- **Retention**: user-level data and conversions deleted at 14 months; age, gender and interest at
  2 months; other events per the property setting (GA360 up to 50 months). Allow ~12 days before
  treating conversions as final — modelled conversions are still settling.
- **Imported Google Ads metrics** are limited to 37 months when combined with any date breakdown.
- **Sampling** on high-cardinality queries over long ranges. Say so rather than reporting it silently.
- **Cohort report types** (`CohortDaily`/`CohortWeekly`/`CohortMonthly`) use `dailyCohort`,
  `weeklyCohort`, `monthlyCohort`, `cohortNth*` and don't mix with default-report fields.
- **`session` vs `firstUser` vs unprefixed** attribution answer "where did this come from" differently
  (last-click for the session, first-touch for the user, event-scoped). Say which you used.
- **No segments and no attribution modelling** in the Data API. Use `filters`, and say so.
- Quotas per property: 200k tokens/day and 40k/hour (GA360 10×), 10 concurrent requests. Day-by-day
  pulls burn these fast.
- **GA4 will not match the ad platforms** — different attribution, time zones and click-vs-session
  definitions. Present both views; don't declare one wrong.

## Analysis

- Quality over volume: engagement rate, pages per session and conversion rate by channel. A channel
  that doubled sessions and halved engagement rate got worse.
- Landing pages: where entries land, and where engaged sessions become key events.
- Channel mix via `sessionDefaultChannelGrouping`, then `sessionSourceMedium` for whatever moved.
- Ecommerce funnel: item views → add to cart → checkout → purchase, with `cartToViewRate` and
  `purchaseToViewRate` locating the drop.
- Compare with `compare_type="prev_range"` or `"prev_year"`.
- Most recent day is usually incomplete; some processing lags 24–48h.

Paired with an ads connector, GA4 answers what the traffic did after the click — use it to qualify a
platform's conversion claims, not to overrule them silently.

## Common requests

- **"How is the site doing?"** → `sessionDefaultChannelGrouping` + `sessions` `engagedSessions`
  `engagementRate` `conversions`, with `compare_type="prev_range"`. A channel that doubled sessions
  and halved engagement rate got worse.
- **"Which pages bring people in?"** → `landingPage` + `sessions` `engagementRate` `conversions`.
  Sort by entries, then read the conversion column, not the traffic column.
- **"Where does checkout break?"** → `itemViews` `addToCarts` `checkouts` `ecommercePurchases`
  `cartToViewRate` `purchaseToViewRate` on one row.
- **"What changed this week?"** → `date` + `sessions` `conversions` with
  `compare_type="prev_range"`, then re-query the one channel that moved by `sessionSourceMedium`.
- **"Is our paid traffic any good?"** → `sessionCampaignName` + `sessions` `engagementRate`
  `conversions`, filtered to paid mediums. Compare against the ad platform's own conversions and
  present both — different attribution, so do not declare one wrong.
- **"Which key events fire?"** → `eventName` + `eventCount`, or this property's `c_keyevents_*`
  fields from `field_discovery`. Never assume a key event name.

## Querying

`filters` — `sessions > 100`, `country == US AND sessions > 100`. **Operators need a space either
side**; `sessions>100` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=`
`=@` `!@` `=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs — `screenPageViews` renders as "Views". Map by
  `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric. Report what actually came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.
- A `currency_recommendation` means monetary columns carry no currency — add `currencyCode`.

## Campaign management

Supermetrics write-back — creating, editing, pausing and budgeting campaigns — works on the **paid
ads connectors** (Google Ads, Meta, Microsoft, TikTok, LinkedIn), not GA4. GA4 is read-only.

When GA4 analysis points to an action on a specific ad platform, you may say the change can be made
through Supermetrics if the user has that connector. Never imply GA4 itself can be written to, and
never report a change as made unless a write tool returned success.

## Failures

| Symptom | Do |
|---|---|
| Rejected: >9 dimensions or >10 metrics | Drop fields to the limit and re-run |
| Incompatible dimension/metric pair | `field_discovery(ds_id="GAWA", filter=…)`, simplify, add fields back one at a time |
| `Selected field combination is invalid` | Report-type conflict; the message names no field. Compare `report_types`, split the query |
| Unknown field | `field_discovery` — custom key events are property-specific, never assumed |
| Property not found / no access | `accounts_discovery(ds_id="GAWA")` |
| Auth error | `data_source_discovery(ds_id="GAWA")`, surface `login_link` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Sampling, thresholding, partial data | `supermetrics_guide(mode="ga4_data")` |
| Disagrees with the ad platform | `supermetrics_guide(mode="data_discrepancies")` |

## Docs

[Fields](https://docs.supermetrics.com/docs/google-analytics-4-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-google-analytics-4) ·
[Report building](https://docs.supermetrics.com/docs/google-analytics-4-report-building-guide)
