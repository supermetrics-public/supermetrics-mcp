---
name: google-ads
description: Query and manage Google Ads through Supermetrics. Use for ad spend, ROAS, CPA, conversions, CTR, impression share, quality score, keywords, search terms, Performance Max, Shopping, ad group and campaign performance, budget pacing, and for creating, editing, pausing or budgeting Google Ads campaigns.
---

# Google Ads with Supermetrics

`AW` · accounts are **ad accounts** · date range always required.

## Rules

- **Never invent.** Every number, campaign, ad group, keyword, search term and date must come from a
  tool response in this conversation. No estimates, no illustrative examples, no "typical" benchmarks.
  Empty result → say it was empty. Can't retrieve it → say what failed.
- **Never guess a field ID.** A wrong guess is not safely rejected: Supermetrics may alias it to a
  *different* metric and return data, so a bad guess reads as a right answer. IDs come from
  `field_discovery`; confirm with `canonical_field_ids` in the response.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="AW")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="AW")`.
3. `manage_business_context(action="get", ds_id="AW", account_id=…)` — targets, exclusions, naming
   rules. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="AW", filter="…")` — always filter; there are 548 fields.
5. `data_query(…)` — dimensions before metrics.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Metrics** `Impressions` `Clicks` `Cost` (account currency; `Cost_eur|gbp|usd` converted) `Ctr` `CPC`
`CPM` `Conversions` `ConversionValue` `ConversionRate` `CostPerConversion` `ROAS` `interactions`
`Viewthroughconversions` `AllConversionRate`
**Impression share** `SearchImpressionShare` `SearchBudgetLostImpressionShare`
`SearchRankLostImpressionShare` `SearchAbsoluteTopImpressionShare` `SearchTopImpressionShare`
`AbsoluteTopImpressionPercentage` `SearchClickShare` `ContentImpressionShare`
**Video** `videoviews` `videoviewrate` `VideoQuartile25Rate`–`VideoQuartile100Rate` `AverageCpv`
**Budget** `Budget` `dailybudget` `budgetUsedPortion`
**Dimensions** `Date` `Currencycode` `Campaignname` `CampaignID` `Campaignstatus`
`AdvertisingChannelType` `Adgroupname` `AdgroupID` `AdID` `Adtype` `ad_strength` `Keyword` `Matchtype`
`Searchterm` `branded_vs_nonbranded` `Device` `Network` `Country` `City` `Biddingstrategy` `TargetCPA`
`TargetROAS` `Qualityscore` `SearchPredictedCtr` `ConversionTypeName` `Labels` `MaxCPC`
`OptimizationScore` `utm_campaign`

`Qualityscore` is a **dimension**; `HistoricalQualityScore` is the metric.

## Good to know

- **41 report types.** All fields in a query must share one — verified: `Searchterm` + `Qualityscore`
  fails. Check `report_types` overlap in `field_discovery`.
- **Non-aggregatable**: `Ctr`, `CPC`, `ROAS`, impression share, quality scores. Each row is correct;
  never sum or average them across rows — re-query at the grouping you want.
- **37 months** for hourly/daily/weekly breakdowns; older data needs `Yearmonth`/`Year` with
  whole-month boundaries. `unique_users` 3 years. ClickView 90 days.
- **Cost, budget, keyword, ClickView, location and conversion-action fields are fetched with a date
  breakdown underneath.** Rows still return at the grouping you asked for (campaign-level cost = one
  row per campaign), but the 37-month limit applies regardless and wide ranges cost more than they look.
- **Settings**: `asset_level`; `brand_keywords` (must be set before `branded_vs_nonbranded` means
  anything); `include_zero_impressions` (the API drops zero-impression rows by default); `geo_view`;
  `exclude_invalid_accounts`; `targeting_expansion`.
- **Performance Max** reports through asset groups — no ad group or keyword rows.
- `Conversions` is click-dated, `conversionsByConversionDate` conversion-dated. Say which.
- Not retrievable: experiment campaigns, calculated custom columns, user-set attribution models.
  Parent-account labels don't reach child accounts. Conversion values drift as breakdowns get finer.

## Analysis

- CPA/ROAS against the account's own history and the targets in business context.
- `SearchBudgetLostImpressionShare` → add budget. `SearchRankLostImpressionShare` → fix bids or
  quality. Different actions; don't conflate them.
- Waste: search terms with spend and no conversions; low quality score on real spend; campaigns
  spending with zero conversions.
- Compare with `compare_type="prev_range"` or `"prev_year"` in the same query, not two.
- Flag: incomplete final day; data stopping mid-range (usually a broken connection, not a zero);
  conversions still inside the lag window.

## Common requests

- **"How are we doing?"** → `Campaignname` + `Cost` `Impressions` `Clicks` `Conversions`
  `CostPerConversion` `ConversionValue` `ROAS`, with `compare_type="prev_range"`. Lead with what
  moved, not the table.
- **"Where are we wasting money?"** → `Searchterm` `Matchtype` + `Cost` `Clicks` `Conversions`,
  `filters="Conversions == 0 AND Cost > 50"`. The answer is a negative-keyword list.
- **"Can we spend more?"** → `Campaignname` + `SearchImpressionShare`
  `SearchBudgetLostImpressionShare` `SearchRankLostImpressionShare` `Cost`. Budget-lost → raise
  budget. Rank-lost → fix bids or quality. Report them separately.
- **"Brand vs non-brand"** → `branded_vs_nonbranded` + `Cost` `Conversions` `CostPerConversion`.
  Needs `brand_keywords` set in `settings`, or every row reads the same.
- **"What should we scale?"** → campaign CPA/ROAS against the account average, restricted to
  campaigns with headroom (`SearchBudgetLostImpressionShare > 0.1`).
- **"Pause the losers"** → read with `campaign_and_resource_get`, name the campaigns and their spend,
  get explicit approval, then `manage_campaign` with `status: "PAUSED"`.

## Querying

`filters` — `Clicks > 5`, `country == US AND clicks > 100`. **Operators need a space either side**;
`Clicks>5` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=` `=@` `!@`
`=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs. Map by `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric. Report what actually came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.
- A `currency_recommendation` means monetary columns carry no currency — add `Currencycode`.

## Write-back

Supermetrics writes to Google Ads. Offer it rather than sending the user to the UI.

Read first with `campaign_and_resource_get(ds_id="AW", account_id=…)`. Never update what you haven't read.

`manage_campaign(ds_id="AW", account_id=…)`:

- **Create** — omit `campaign_id`; `budget_amount` required; set `platform_settings.campaign_type`
  (`SEARCH`|`DISPLAY`|`SHOPPING`|`PERFORMANCE_MAX`|`DEMAND_GEN`|`MULTI_CHANNEL`); build
  `ad_groups[].ads[].creative`.
- **Update** — pass `campaign_id`; only sent fields change; campaign type is fixed at creation.
- **Creative** — 3–15 headlines (30 chars), 2–4 descriptions (90), `final_urls` required. Display adds
  `long_headline` + `business_name`. PMax images must be 1.91:1, 1:1 or 4:5; 16:9 and 9:16 are
  rejected. Media via `asset_url` | `asset_id` | `upload_ref` from `creative_picker`.
- **Keywords** — `add_keywords`/`remove_keywords` append and remove; `targeting.keywords` **replaces
  the whole set**; negatives go in `targeting.negative_keywords`, never in `keywords`.
- **`targeting` replaces, never merges.** Send the full set or you delete what you omit.

Safety:

- Write access is enabled per account at
  [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings) with an approval
  level. Permission errors are almost always this.
- **New campaigns are always created paused.** Set `status: "ENABLED"` only on explicit approval of
  that campaign — it starts spend immediately.
- **PMax needs two approvals**: the campaign, then its asset groups, which stay paused separately.
- Confirm budget in writing first: amount, currency, daily vs lifetime.
- Check `write_status` and `failures`; partial success happens, and blind retries duplicate.
- Google bug: new keywords can report success without being added — tell the user to verify in the UI.

Logged at [hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).
Re-read after writing; state what changed and what is still paused.

**Planning** (read-only): `campaign_and_resource_get` also serves `keyword_ideas`, `keyword_volumes`,
`forecast`, `targeting_search`, `audiences`, `recommendations`, `history`, `assets`, `conversion_types`.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-type conflict; the message names no field. Compare `report_types`, split the query. Not an account or date problem |
| `[FIELD_NOT_FOUND]` | `field_discovery(ds_id="AW", filter=…)`, retry |
| Account not found / no access | `accounts_discovery(ds_id="AW")` |
| Auth error | `data_source_discovery(ds_id="AW")`, surface `login_link` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with the Google Ads UI | `supermetrics_guide(mode="data_discrepancies")` |
| Write rejected | `supermetrics_guide(mode="write_access")` |
| Quota / row limit | `supermetrics_guide(mode="quotas_and_limits")` |

## Docs

[Fields](https://docs.supermetrics.com/docs/google-ads-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-google-ads) ·
[Report building](https://docs.supermetrics.com/docs/google-ads-report-building-guide) ·
[Campaign management](https://docs.supermetrics.com/docs/how-to-manage-ad-campaigns-with-ai-tools)
