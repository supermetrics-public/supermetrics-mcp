---
name: linkedin-ads
description: Query and manage LinkedIn Ads through Supermetrics. Use for B2B ad spend, leads and cost per lead, lead gen form performance, CTR and CPC, engagement, video and document ad metrics, member firmographics (company size, industry, seniority, job function, job title), CRM-attributed revenue, and for creating, editing, pausing or budgeting LinkedIn campaigns.
---

# LinkedIn Ads with Supermetrics

`LIA` · accounts are **ad accounts** · date range always required.

## Rules

- **Never invent.** Every number, campaign, creative, company name, job title and date must come from
  a tool response in this conversation. No estimates, no illustrative examples, no "typical" B2B
  benchmarks. Firmographics name **real companies and real job titles** — quote them exactly, never
  tidy them up. Empty result → say it was empty.
- **Never guess a field ID.** A wrong guess is not safely rejected: a loose name like `leads` or
  `spend` can alias to a metric with a different definition. IDs come from `field_discovery`; confirm
  with `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## LinkedIn's hierarchy is off by one

| LinkedIn | Elsewhere | Fields |
|---|---|---|
| Campaign Group | Campaign | `campaignGroupName`, `campaignGroupId`, `campaignGroupTotalBudget` |
| Campaign | Ad set / ad group | `campaignName`, `campaignId`, `campaignDailyBudget` |
| Creative | Ad | `creativeId`, `creativeTitle`, `creativeText` |

"Campaign" meaning the top level is usually `campaignGroupName`. Confirm which they mean, and say
which level your numbers are at.

## Workflow

1. `data_source_discovery(ds_id="LIA")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="LIA")`.
3. `manage_business_context(action="get", ds_id="LIA", account_id=…)` — target cost per lead, brand
   vs demand gen, how the team defines a qualified lead. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="LIA", filter="…")` — filter; 186 fields.
5. `data_query(…)` — dimensions before metrics.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Spend / delivery** `spend` (account currency; `spend_eur|gbp|usd` converted) `impressions` `clicks`
`ctr` `cpc` `cpm` `approximateUniqueImpressions` (Reach) `audience_penetration` `average_dwell_time`
**Leads — the metric that matters here** `oneClickLeads` `oneClickLeadsCost` `oneClickLeadFormOpens`
`leadFormCompletionRate` `viralOneClickLeads` `talent_leads` `cost_per_talent_lead`
**Conversions** `conversions` `postClickConversions` `viewThroughConversions` `conversionRate`
`conversionCost` `conversionValue` `returnOnSpend`
**CRM revenue** (report type `attributed_revenue_analytics`, needs a CRM linked) `rar_revenue_won`
`rar_return_on_ad_spend` `rar_closed_won_opportunities` `rar_open_opportunities`
`rar_opportunity_win_rate` `rar_average_deal_size` `rar_average_days_to_close`
**Engagement** `totalEngagements` `engagementRate` `totalSocialActions` `likes` `comments` `shares`
`follows` `reactions` `companyPageClicks` `landingPageClicks` + the parallel `viral*` family
**Video / document** `videoViews` `videoStarts` `videoCompletions` `videoFirstQuartileCompletions`
`videoMidpointCompletions` `videoThirdQuartileCompletions` `fullScreenPlays` `documentCompletions`
`downloadClicks`
**Firmographics — LinkedIn's real advantage** `memberCompanySize` `memberIndustry` `memberSeniority`
`memberJobTitle` `memberJobFunction` `memberCompanyName` `memberCountry` `memberRegion`
**Dimensions** `date` `accountName` `accountCurrencyCode` (add whenever reporting spend)
`campaignGroupName` `campaignName` `campaignStatus` `campaignType` `campaignObjectiveType`
`campaignCostType` `campaignDailyBudget` `campaignTotalBudget` `creativeId` `creativeTitle`
`creativeText` `impressionDeviceType` `servingLocation` `conversionName` `adformName` `utm_campaign`

## Good to know

- **8 report types** (`ad_analytics`, `ad_analytics_campaign`, `ad_analytics_chunkless`, `ad_form`,
  `ad_form_responses`, `ad_statistics`, `ad_statistics_chunkless`, `attributed_revenue_analytics`).
  All fields in a query must share one — the `rar_*` and `adForm*` families each sit alone.
- **Demographic dimensions are heavily restricted**:
  - **One per query.** Verified: `memberSeniority` + `memberIndustry` fails. Seniority and industry
    are two queries.
  - They combine only with **campaign name/ID and account level** — not creative, ad form or
    conversion dimensions. So there is no creative-level firmographic breakdown.
  - **Reach does not work with any demographic dimension, and it fails silently.** Verified: the
    query succeeds and every Reach cell returns `null` while other metrics populate normally. Never
    report a null Reach as zero reach. Reach also needs a range of **92 days or less** to work at all.
  - No carousel or video metrics on demographic pivots. Daily demographic data covers 6 months;
    coarser granularities 2 years. Figures are **approximate by design** — present as directional.
- **Conversion dimensions only work with conversion metrics.** `conversionName` + `impressions`
  returns empty values.
- **Non-aggregatable**: `ctr`, `cpc`, `cpm`, `engagementRate`, `conversionRate`, `conversionCost`,
  `oneClickLeadsCost`, `audience_penetration`, `returnOnSpend`. Never sum or average across rows.
- **LinkedIn runs in UTC only** — no account time zone, so daily figures offset against platforms
  reporting locally.
- Small demographic segments are suppressed for privacy. A missing segment isn't a zero.
- CPMs are high by design. Never benchmark them against Meta or Google — compare cost per lead and
  pipeline quality.

## Analysis

- Cost per lead and `leadFormCompletionRate` before CTR. High form opens with low completion = the
  form is too long, not the targeting.
- Break out `memberSeniority`, `memberJobFunction`, `memberCompanySize` and check delivery against
  the intended ICP. Spend landing on the wrong seniority is the most common LinkedIn waste.
- `memberCompanyName` shows which companies engage — valuable for ABM, and the reason to quote names exactly.
- Check `servingLocation` for Audience Network leakage: cheaper, much lower intent.
- `rar_revenue_won` beats platform conversions for B2B — the sales cycle outlives the attribution
  window. Say so explicitly: recent spend hasn't had time to close, so current-period ROAS reads low.
- Compare with `compare_type="prev_range"` or `"prev_year"`.

## Common requests

- **"How are we doing?"** → `campaignGroupName` + `spend` `impressions` `clicks` `ctr` `oneClickLeads`
  `oneClickLeadsCost`, with `compare_type="prev_range"`. Campaign group is what they mean by campaign.
- **"What is a lead costing us?"** → `campaignName` + `spend` `oneClickLeads` `oneClickLeadsCost`
  `leadFormCompletionRate`. High form opens with low completion = the form is too long.
- **"Are we reaching the right people?"** → `memberSeniority` + `impressions` `clicks` `spend`.
  Then a **separate** query for `memberJobFunction` — one demographic dimension per query, and drop
  Reach or it returns silent nulls.
- **"Which companies are engaging?"** → `memberCompanyName` + `impressions` `clicks`. The ABM query.
  Quote the company names exactly as returned.
- **"Are we leaking to Audience Network?"** → `servingLocation` + `spend` `clicks` `oneClickLeads`.
  Off-LinkedIn delivery is cheaper and much lower intent.
- **"Did any of it close?"** → `campaignGroupName` + `rar_revenue_won` `rar_return_on_ad_spend`
  `rar_closed_won_opportunities` (needs a CRM linked). Say plainly that recent spend has not had time
  to close, so current-period ROAS reads low.

## Querying

`filters` — `clicks > 5`, `campaignName =@ EMEA AND clicks > 100`. **Operators need a space either
side**; `clicks>5` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=`
`=@` `!@` `=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs. Map by `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric. Report what actually came back.
- `null` is not 0. Say "no data" — and on LinkedIn a null Reach beside a demographic dimension means
  the combination is unsupported, not that reach was zero.
- A `currency_recommendation` means monetary columns carry no currency — add `accountCurrencyCode`.

## Write-back

Supermetrics writes to LinkedIn Ads. Offer it rather than sending the user to Campaign Manager.

Read first with `campaign_and_resource_get(ds_id="LIA", account_id=…)`. Never update what you haven't read.

`manage_campaign(ds_id="LIA", account_id=…)`:

- **Create** — omit `campaign_id`; `budget_amount` required. Campaign level takes `LIFETIME` budgets;
  `daily_budget`, `campaign_type`, `billing_event`, `objective_type` go in
  `ad_groups[].platform_settings`.
- **Update** — pass `campaign_id`; only sent fields change. Objective and campaign type fixed at creation.
- **Creative** — **single image only** through this tool. `descriptions[0]` = primary text,
  `headlines[0]` = headline. Media via `asset_url` | `asset_id` | `upload_ref` from `creative_picker`.
- **`targeting` replaces, never merges.** Send the full set.

Safety:

- Write access is enabled per account at
  [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings) with an approval
  level. Permission errors are almost always this.
- **New campaigns are always created paused.** `status: "ENABLED"` only on explicit approval of that campaign.
- Confirm budget in writing first: amount, currency, daily vs lifetime.
- Check `write_status` and `failures`; partial success happens, and blind retries duplicate.

Logged at [hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).
Re-read after writing; state what changed and what is still paused.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-type or pivot conflict; the message names no field. Usually two demographic dimensions, or a demographic with a creative dimension. Split the query |
| Reach column all `null` | A demographic dimension is present — drop it, or drop Reach. Not a data gap |
| Unknown field | `field_discovery(ds_id="LIA", filter=…)`, check `report_types` overlap |
| Account not found / no access | `accounts_discovery(ds_id="LIA")` |
| Auth error | `data_source_discovery(ds_id="LIA")`, surface `login_link` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with Campaign Manager | `supermetrics_guide(mode="data_discrepancies")` |
| Ads vs Company Pages confusion | `supermetrics_guide(mode="linkedin_connectors")` |
| Write rejected | `supermetrics_guide(mode="write_access")` |

## Docs

[Fields](https://docs.supermetrics.com/docs/linkedin-ads-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-linkedin-ads) ·
[Report building](https://docs.supermetrics.com/docs/linkedin-ads-report-building-guide) ·
[Campaign management](https://docs.supermetrics.com/docs/how-to-manage-ad-campaigns-with-ai-tools)
