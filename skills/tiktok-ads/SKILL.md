---
name: tiktok-ads
description: Query and manage TikTok Ads through Supermetrics. Use for ad spend, results and cost per result, conversions, ROAS, reach and frequency, video view-through rates and hook retention, creative and ad group performance, Spark Ads, GMV Max and TikTok Shop metrics, SKAN reporting, audience breakdowns, and for creating, editing, pausing or budgeting TikTok campaigns.
---

# TikTok Ads with Supermetrics

`TIK` · accounts are **ad accounts** (advertisers) · date range always required · **at least one
metric required** (`min_metrics: 1`) — a dimensions-only query fails.

## Rules

- **Never invent.** Every number, campaign, ad group, ad, creative and date must come from a tool
  response in this conversation. No estimates, no illustrative examples, no "typical" benchmarks.
  Empty result → say it was empty. Can't retrieve it → say what failed.
- **Never guess a field ID.** 502 fields across standard, SKAN, onsite, offline and GMV Max families,
  and picking the wrong family silently changes the answer. A loose name like `purchases` can alias
  to any of them. IDs come from `field_discovery`; confirm with `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="TIK")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="TIK")`.
3. `manage_business_context(action="get", ds_id="TIK", account_id=…)` — target cost per result, which
   conversion event counts, whether GMV Max is reported separately. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="TIK", filter="…")` — always filter. `filter="purchase"` returns the
   standard, SKAN, onsite, offline and GMV Max variants side by side so you can pick deliberately.
5. `data_query(…)` — dimensions before metrics, at least one metric.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Spend / delivery** `cost` (`cost_eur|gbp|usd` converted) `impressions` `gross_impressions` `clicks`
`ctr` `cpc` `cpm` `reach` `frequency` `cost_per_1000_reached`
**Objective-relative** `results` `result_rate` `cost_per_result` — whatever the ad group was optimised
for. Comparable within an objective, misleading across objectives; always say which objective.
**Conversions** `conversions` `conversion_rate` `cost_per_conversion` `total_purchase`
`total_purchase_value` `complete_payment` `complete_payment_roas` `total_landing_page_view`
`cost_per_landing_page_view` `web_event_add_to_cart` `initiate_checkout` `sales_lead`
`onsite_shopping` `onsite_shopping_roas`
**Video — the core diagnostic** `video_play_actions` `video_watched_2s` `video_watched_6s`
`video_views_p25` `video_views_p50` `video_views_p75` `video_views_p100` `average_video_play`
`average_video_play_per_user` `engaged_view` `engaged_view_15s`
**Organic-style engagement on paid** `engagements` `engagement_rate` `likes` `comments` `shares`
`follows` `profile_visits` `sound_usage_clicks` `duet_clicks` `stitch_clicks`
**GMV Max / TikTok Shop** `gmv_max_orders` `gmv_max_gross_revenue` `gmv_max_roi`
`gmv_max_cost_per_order` `gmv_max_live_views`
**SKAN (iOS)** the `skan_*` family — a separate, **non-additive** view. Never add to standard conversions.
**Dimensions** `date` `advertiser_name` `campaign_name` `campaign_id` `campaign_objective_type`
`campaign_status` `campaign_budget` `ad_group_name` `ad_group_id` `ad_group_status` `optimize_goal`
`billing_event` `bid_type` `placement_type` `adgroup_placement` `ad_name` `ad_id` `ad_text` `video_id`
`country_code` `country_name` `age` `gender` `placement` `platform` `language`
`interest_category_name` `utm_campaign`

## Good to know

- **18 report types** (`Campaign`, `AdGroup`, `Ad`, `Advertiser`, the `*Audience` set, GMV Max and
  Smart+ families). All fields in a query must share one.
- **Audience dimensions only work with basic and attribute metrics.** Verified against the catalogue:
  `cost`, `impressions`, `clicks` and the video quartiles carry the `*Audience` types and **do**
  combine with age, gender, country, placement, interest. `average_video_play_per_user` and
  `engaged_view` do **not** — they cannot be broken out by audience at all. Generic `conversions` and
  `cost_per_conversion` share **no** report type with `country_code`; for conversions by country use
  the specific events (`total_purchase`, `complete_payment`, `sales_lead`, `total_landing_page_view`).
- **`ad_name` can make rows disappear.** It carries only `Ad`, `AdAudience`, `SmartPlusAd` — narrower
  than most metrics — so adding it silently narrows the query. If an ad-level query returns less than
  expected, drop `ad_name`, keep `ad_id`, and see whether rows come back.
- **Spend metrics lag 48 hours.** Never present the last two days as final.
- **Non-aggregatable**: `ctr`, `cpc`, `cpm`, `frequency`, `reach`, every rate and `cost_per_*`. Never
  sum or average across rows — re-query at the grouping you want.
- **Pick one conversion family and stay in it.** Standard, onsite, offline, SKAN and GMV Max all count
  purchases differently; mixing double-counts. Name the family you used.
- Sync API returns up to **365 days**. Reach/frequency beyond that is chunked and summed, breaking
  deduplication — keep reach queries inside 365 days.
- `cost_cash` and `cost_voucher` exist only on the `Advertiser` report type — account level only.
- Custom attribution isn't available via API. Regional accounts need their own connection.
- **Settings**: `include_deleted_items` (slow), `exclude_invalid_accounts`.

## Analysis

- **Hook retention first**: `video_watched_2s` and `video_watched_6s` against `impressions`. A weak
  hook explains more bad TikTok performance than targeting does.
- Retention curve p25 → p50 → p75 → p100 locates where viewers leave.
- **Creative velocity**: rising `frequency` + falling `engagement_rate` + rising `cpm` means new
  creative, not a bid change.
- Cost per result **within** an objective, never across objectives.
- `shares`, `follows`, `profile_visits` show an ad earning attention rather than buying it — often the
  leading indicator of a cheap scaling window.
- Compare standard conversions against the `skan_*` view before concluding iOS underperforms.
- Compare with `compare_type="prev_range"` or `"prev_year"`.
- Flag: incomplete final day, the 48h spend lag, SKAN's delay and coarse granularity, learning phase.

## Common requests

- **"How are we doing?"** → `campaign_name` + `cost` `impressions` `clicks` `ctr` `results`
  `cost_per_result`, with `compare_type="prev_range"`. Name the objective the results belong to, and
  do not treat the last two days as final.
- **"Does the hook work?"** → `ad_id` + `impressions` `video_watched_2s` `video_watched_6s`
  `video_views_p25` `video_views_p50` `video_views_p100`. The first TikTok query worth running —
  most bad performance is a weak opening, not targeting.
- **"Which creative wins?"** → `ad_id` (add `ad_text` or `video_id` for labels, not `ad_name`, which
  can drop rows) + `cost` `cost_per_result` `engagement_rate` `shares`.
- **"Who are we reaching?"** → `age` `gender` or `country_code` + `cost` `impressions` `clicks`.
  Audience report types only: drop `average_video_play_per_user`, `engaged_view`, and generic
  `conversions` — use `total_purchase` or `complete_payment` for conversions by country.
- **"Is it earning attention?"** → `shares` `follows` `profile_visits` against `cost`. Rising organic
  signals on paid often mark a cheap scaling window.
- **"Is iOS underperforming?"** → compare `conversions` against the `skan_*` equivalents in separate
  queries. Never add the two families together.

## Querying

`filters` — `clicks > 5`, `country_code == US AND clicks > 100`. **Operators need a space either
side**; `clicks>5` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=`
`=@` `!@` `=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs. Map by `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric, and on TikTok that can mean a different conversion family. Report what came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.
- A `currency_recommendation` means monetary columns carry no currency — add the currency dimension
  it names.

## Write-back

Supermetrics writes to TikTok Ads. Offer it rather than sending the user to Ads Manager.

Read first with `campaign_and_resource_get(ds_id="TIK", account_id=…)`. Never update what you haven't read.

`manage_campaign(ds_id="TIK", account_id=…)`:

- **Create** — omit `campaign_id`; `budget_amount` required; set `platform_settings.objective_type`
  (`TRAFFIC`|`CONVERSIONS`|`REACH`|`LEAD_GENERATION`|`VIDEO_VIEWS`) and `budget_optimize_switch`
  (`ON` = budget at campaign level, so ad groups don't need their own).
- **Ad groups** take `optimization_goal` (`CLICK`|`IMPRESSION`|`REACH`|`ENGAGED_VIEW`|`CONVERSION`|
  `INSTALL`), `billing_event` (`CPC`|`CPM`|`CPV`), `placement_type`, `placements`. `REACH` also needs
  a `frequency` cap.
- **Video creatives must use `asset_id`** — a public `asset_url` will not work, and this is the most
  common creation failure. Upload via `creative_picker` first. Spark Ads use `tiktok_item_id` in the
  ad's `platform_settings`.
- **Targeting needs at least one location** (ISO country codes) and **replaces, never merges**.
- **Update** — pass `campaign_id`; only sent fields change. Objective fixed at creation.

Safety:

- Write access is enabled per account at
  [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings) with an approval
  level. Permission errors are almost always this.
- **New campaigns are always created paused.** `status: "ENABLED"` only on explicit approval of that campaign.
- Confirm budget in writing first: amount, currency, daily vs lifetime, campaign vs ad group.
- Check `write_status` and `failures`; partial success happens, and blind retries duplicate.

Logged at [hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).
Re-read after writing; state what changed and what is still paused.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-type conflict; the message names no field. Compare `report_types`, split the query. Not an account or date problem |
| Audience dimension rejected | Query the audience breakdown separately, and drop metrics that lack the `*Audience` types |
| Fewer ad-level rows than expected | Drop `ad_name`, keep `ad_id` |
| Rejected with no metric | Add at least one metric |
| Unknown field | `field_discovery(ds_id="TIK", filter=…)` |
| Account not found / no access | `accounts_discovery(ds_id="TIK")` |
| Auth error | `data_source_discovery(ds_id="TIK")`, surface `login_link` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with Ads Manager | `supermetrics_guide(mode="data_discrepancies")` — check which conversion family you used |
| Ads vs Organic connector confusion | `supermetrics_guide(mode="tiktok_connectors")` |
| Write rejected, or video creative refused | `supermetrics_guide(mode="write_access")`; confirm you used `asset_id`, not a URL |

## Docs

[Fields](https://docs.supermetrics.com/docs/tiktok-ads-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-tiktok-ads) ·
[Report building](https://docs.supermetrics.com/docs/tiktok-ads-report-building-guide) ·
[Smart Ads & GMV Max](https://docs.supermetrics.com/docs/tiktok-ads-smart-ads-and-gmv-max-campaign-reporting-august-10-2026) ·
[Campaign management](https://docs.supermetrics.com/docs/how-to-manage-ad-campaigns-with-ai-tools)
