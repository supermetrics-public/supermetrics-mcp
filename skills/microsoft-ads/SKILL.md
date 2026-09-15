---
name: microsoft-ads
description: Query and manage Microsoft Advertising (Bing) through Supermetrics. Use for ad spend, conversions, revenue and ROAS, CPC and CTR, impression share and click share, quality score, keywords, search queries, Shopping and product performance, audience and device breakdowns, and for creating, editing, pausing or budgeting Microsoft Advertising campaigns.
---

# Microsoft Advertising with Supermetrics

`AC` · accounts are **ad accounts** · date range always required.

## Rules

- **Never invent.** Every number, campaign, ad group, keyword, search query and date must come from a
  tool response in this conversation. No estimates, no illustrative examples, no "typical" benchmarks.
  Empty result → say it was empty. Can't retrieve it → say what failed.
- **Never guess a field ID.** Cost is `Spend` here, not `Cost`. A wrong guess is not safely rejected:
  a loose name can alias to a metric with a different definition, and `Conversions` vs
  `AllConversions` are not the same number. IDs come from `field_discovery`; confirm with
  `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="AC")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="AC")`.
3. `manage_business_context(action="get", ds_id="AC", account_id=…)` — target CPA/ROAS, which goals
   count, exclusions. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="AC", filter="…")` — filter; 163 fields.
5. `data_query(…)` — dimensions before metrics.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Core** `Impressions` `Clicks` `Spend` (cost, account currency; `Spend_usd|gbp|eur` converted) `Ctr`
`Cpc` `Cpm`
**Conversions / value** `Conversions` `AllConversions` `ConversionRate` `AllConversionRate`
`CostPerConversion` `AllCostPerConversion` `Revenue` `AllRevenue` `ROAS` `RevenuePerConversion`
`RevenueMinusSpend` `Assists` `CostPerAssist`
**Impression share** `ImpressionSharePercent` `ExactMatchImpressionSharePercent`
`ImpressionsLostToBudget` `ImpressionLostToBudgetPercent` `ImpressionsLostToRankAgg`
`ImpressionLostToRankAggPercent` `ClickSharePercent` `AvailableImpressions`
`TopImpressionSharePercent` `AbsoluteTopImpressionSharePercent`
**Quality** `QualityScore` `ExpectedCtr` `AdRelevance` `HistoricQualityScore` `QualityImpact`
**Other** `PhoneCalls` `Ptr` `video_views` `view_through_rate` `video_views_p25`–`video_views_p100`
`video_completion_rate`, plus the `LowQuality*` family for invalid traffic
**Dimensions** `Date` `AccountName` `AccountId` `CustomerName` (manager) `CurrencyCode` `CampaignName`
`CampaignId` `CampaignType` `CampaignStatus` `AdGroupName` `AdGroupId` `AdId` `AdTitle` `AdType`
`Keyword` `KeywordStatus` `BidMatchType` `DeliveredMatchType` `CurrentMaxCpc` `SearchQuery`
`NegativeKeyword` `ConflictLevel` `DeviceType` `DeviceOS` `Network` `AdDistribution` `TopVsOther`
`Goal` `AudienceName` `AgeGroup` `Gender` `FinalURL` `ProductGroup` `MerchantProductId` `BudgetName`

`AllConversions` includes goals excluded from the headline `Conversions` metric, so the two diverge
on accounts with many goals. Say which you used; if the gap is large, check `Goal` in
`ConversionPerformance` rather than guessing.

## Good to know

- **28 report types**, and all fields in a query must share one. This is the most common failure here.
  Verified from the catalogue:
  - `SearchQuery` → `SearchQueryPerformance` and `ProductSearchQueryPerformance` only.
  - `QualityScore`, `ExpectedCtr`, `AdRelevance` → ad group, campaign, keyword and share-of-voice
    reports; **not** the ad report.
  - `ClickSharePercent` → `ShareOfVoice` only.
  - `AgeGroup`, `Gender` → `AgeGenderAudience` only. `AudienceName` → `AudiencePerformance` only.
  - `NegativeKeyword`, `ConflictLevel` → `NegativeKeywordConflict` only.
- **Non-aggregatable**: `Ctr`, `Cpc`, `Cpm`, `ConversionRate`, `CostPerConversion`, `ROAS`, all
  impression-share percentages, all historic averages. Never sum or average across rows.
- **Import parity is not data parity.** Most accounts are imported from Google Ads, so structure and
  names match while volume, CPCs and conversions do not. Never present Microsoft numbers as
  validation of Google numbers.
- **MFA is required** on the Microsoft user account for API access. An auth failure on an account that
  works in the browser is usually MFA or a pending admin approval.
- `exclude_invalid_accounts` in `settings` for multi-account pulls.

## Analysis

- Efficiency against the account's own history and business-context targets. Low volume makes small
  absolute changes look dramatic in percentage terms — give absolutes alongside.
- Headroom: `ImpressionSharePercent` with `AvailableImpressions`.
  `ImpressionLostToBudgetPercent` → add budget. `ImpressionLostToRankAggPercent` → fix bids or
  quality. Different actions; don't conflate.
- Search query mining: `SearchQuery` with spend and no conversions is a negative-keyword list. Check
  `NegativeKeywordConflict` for negatives blocking keywords you're paying for.
- Low `QualityScore` on high-spend keywords; `LandingPageExperience` when relevance looks fine but
  score doesn't.
- `AdDistribution` and `Network` separate search from audience network and syndicated partners, which
  behave very differently. `DeviceType`/`DeviceOS` matter more here given the desktop and Windows skew.
- Compare with `compare_type="prev_range"` or `"prev_year"`.

## Common requests

- **"How are we doing?"** → `CampaignName` + `Spend` `Impressions` `Clicks` `Ctr` `Conversions`
  `CostPerConversion` `ROAS`, with `compare_type="prev_range"`. Give absolute numbers alongside
  percentages — low volume makes small moves look dramatic.
- **"Where are we wasting money?"** → `SearchQuery` + `Spend` `Clicks` `Conversions`,
  `filters="Conversions == 0 AND Spend > 25"`. `SearchQueryPerformance` only, so query it alone.
- **"Can we spend more?"** → `CampaignName` + `ImpressionSharePercent` `AvailableImpressions`
  `ImpressionLostToBudgetPercent` `ImpressionLostToRankAggPercent`. Budget-lost and rank-lost mean
  different actions.
- **"Which keywords are dragging?"** → `Keyword` + `QualityScore` `ExpectedCtr` `AdRelevance` `Spend`
  `Conversions`, sorted by spend. Keyword report — will not combine with ad-level fields.
- **"How does this compare to Google?"** → run both, present side by side, and say explicitly that
  imported structure does not mean comparable volume, CPCs or conversions.
- **"Where is the traffic coming from?"** → `AdDistribution` `DeviceType` + `Spend` `Conversions`.
  Search, audience network and syndicated partners behave very differently here.

## Querying

`filters` — `Clicks > 5`, `DeviceType == Computer AND Clicks > 100`. **Operators need a space either
side**; `Clicks>5` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=`
`=@` `!@` `=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs — `Spend` renders as "Cost". Map by
  `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric. Report what actually came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.
- A `currency_recommendation` means monetary columns carry no currency — add `CurrencyCode`.

## Write-back

Supermetrics writes to Microsoft Advertising. Offer it rather than sending the user to the UI.

Read first with `campaign_and_resource_get(ds_id="AC", account_id=…)`. Never update what you haven't read.

`manage_campaign(ds_id="AC", account_id=…)`:

- **Create** — omit `campaign_id`; `budget_amount` required; set `platform_settings.campaign_type`
  (`SEARCH`|`AUDIENCE`|`SHOPPING`|`PERFORMANCE_MAX`). Bidding: `MAX_CLICKS`, `MAX_CONVERSIONS`,
  `MANUAL_CPC`, `ENHANCED_CPC`, `TARGET_CPA`, `TARGET_ROAS`.
- **Update** — pass `campaign_id`; only sent fields change. Campaign type fixed at creation.
- **Creative** — responsive-search shape like Google: multiple headlines (30 chars), descriptions
  (90), `final_urls` required.
- **Keywords** — `add_keywords`/`remove_keywords` append and remove; `targeting.keywords` **replaces
  the whole set**; negatives go in `targeting.negative_keywords`.
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

**Planning** (read-only): `keyword_ideas`, `keyword_volumes`, `audiences`, `recommendations`, `history`.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-type conflict; the message names no field. Compare `report_types`, split into one query per report type. Not an account or date problem |
| Unknown field | `field_discovery(ds_id="AC", filter=…)` — remember cost is `Spend` |
| Account not found / no access | `accounts_discovery(ds_id="AC")` |
| Auth error, or "need admin approval" | `data_source_discovery(ds_id="AC")`, surface `login_link`; then `supermetrics_guide(mode="microsoft_connectors")` — check MFA |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with the Microsoft UI | `supermetrics_guide(mode="data_discrepancies")` |
| Write rejected | `supermetrics_guide(mode="write_access")` |

## Docs

[Fields](https://docs.supermetrics.com/docs/microsoft-advertising-bing-fields) ·
[Connector](https://docs.supermetrics.com/docs/microsoft-advertising-bing) ·
[Report building](https://docs.supermetrics.com/docs/microsoft-advertising-report-building-guide) ·
[Specific conversions](https://docs.supermetrics.com/docs/how-to-query-for-specific-conversions-in-microsoft-advertising-data) ·
[Campaign management](https://docs.supermetrics.com/docs/how-to-manage-ad-campaigns-with-ai-tools)
