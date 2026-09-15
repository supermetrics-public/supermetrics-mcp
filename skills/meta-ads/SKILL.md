---
name: meta-ads
description: Query and manage Facebook and Instagram (Meta) Ads through Supermetrics. Use for ad spend, ROAS, purchases, leads, CPA, CPM, reach and frequency, placement and platform breakdowns, creative and ad set performance, Advantage+ campaigns, attribution windows, and for creating, editing, pausing or budgeting Meta campaigns.
---

# Meta Ads with Supermetrics

`FA` (Facebook **and** Instagram placements) · accounts are **ad accounts**, `act_123…` · date range
always required.

## Rules

- **Never invent.** Every number, campaign, ad set, ad, creative and date must come from a tool
  response in this conversation. No estimates, no illustrative examples, no "typical" benchmarks.
  Empty result → say it was empty. Can't retrieve it → say what failed.
- **Never guess a field ID.** Meta's UI names and Supermetrics IDs differ constantly, and a wrong
  guess is not safely rejected: `purchases` silently resolves to `action_omni_purchase` (web + app +
  in-store), not website purchases. IDs come from `field_discovery`; confirm with
  `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="FA")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
2. `accounts_discovery(ds_id="FA")`.
3. `manage_business_context(action="get", ds_id="FA", account_id=…)` — attribution window,
   exclusions, Advantage+ reporting, target ROAS. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="FA", filter="…")` — always filter; 226 fields.
5. `data_query(…)` — dimensions before metrics.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## Fields

**Spend / delivery** `cost` (Amount spent, account currency; `cost_eur|gbp|usd` converted)
`impressions` `reach` `Frequency` `CPM` `CPP` `Clicks` (all) `action_link_click` (link clicks)
`outbound_clicks` `CPC` `CPLC` `unique_link_CTR`
**Conversions** `offsite_conversions_fb_pixel_purchase` (website purchases)
`offsite_conversion_value_fb_pixel_purchase` `website_purchase_roas` `purchase_conversion_value`
`action_omni_purchase` `on_facebook_purchases` `offsite_conversions_fb_pixel_lead`
`onsite_conversion.lead_grouped` `landing_page_views` `cost_per_landing_page_view`
`cost_per_website_purchase` `cost_per_website_lead` `ROAS`
**Engagement / video** `action_post_engagement` `action_page_engagement` `action_video_view` (3s)
`video_thruplay_watched_actions` `video_p25_watched_actions`–`video_p100_watched_actions`
`video_average_watch_time` `cost_per_thruplay`
**Diagnostics** `quality_score_organic` (Quality ranking) `quality_score_ectr` (Engagement rate
ranking) `quality_score_ecvr` (Conversion rate ranking)
**Dimensions** `Date` `currency` `adcampaign_name` `adcampaign_id` `campaignobjective`
`campaignstatus` `campaign_daily_budget` `campaign_lifetime_budget` `campaign_budget_remaining`
`adset_name` `adset_id` `adsetstatus` `ad_name` `publisher_platform` (facebook | instagram |
audience_network | messenger | threads) `platform_position` `impression_device` `Age` `Gender`
`Countryname` `Region` `attribution_setting` `user_segment_key` (Advantage+ audience)
`creative_image_url` `promoted_page_name` `utm_campaign`

Split Instagram from Facebook by breaking out or filtering on `publisher_platform`.

## Good to know

- **Attribution window is a setting, not a field.** `settings.conversion_window` takes `7D_CLICK`,
  `1D_CLICK`, `7D_CLICK;1D_VIEW`, `28D_CLICK` and others; empty = the ad account's own setting.
  It changes every conversion number. **Always state which window you used** — a window mismatch is
  the usual reason numbers don't match Ads Manager. 28-day options are historical only.
- **Non-aggregatable**: `reach`, `Frequency`, `CPM`, `ROAS`, every `cost_per_*` and every rate. Each
  row is correct; never sum or average across rows — re-query at the grouping you want.
- **Reach never adds up.** Reach across ad sets isn't the sum of ad set reach, and reach across
  multiple ad accounts is summed rather than deduplicated — query reach one account at a time.
- **Custom conversions are account-scoped, not report-type-locked.** `c_action_*`, `c_cost_*`,
  `c_action_value_*` mix freely with standard fields (verified). But each is tied to specific accounts
  via `account_ids`; on another account the query fails `[FIELD_NOT_FOUND]` and then suggests a field
  **identical to the one you sent** — that's the tell that it exists but belongs elsewhere, not that
  you mistyped.
- **History**: 37 months for ad insights; 13 months for reach, unique counts and hourly breakdowns.
- Zero-impression items don't return at all. Creative and preview URLs **expire, often within 24h**.
- Since 6 Aug 2026 `impression_device` and viewer-time-zone hour need opt-in in Ads Manager and only
  return data from the enabling date onward, never retroactively.
- Lead response data expires at 90 days but lead conversion data persists 2 years, so older ranges
  stop lining up. Instagram boosted posts and Ads Manager custom metrics aren't available via API.
- **Settings**: `exclude_zero_impressions` (speed), `include_deleted_items` (slow),
  `exclude_invalid_accounts`.

## Analysis

- ROAS/CPA against the account's own trend and business-context targets, not industry benchmarks.
- **Fatigue**: rising `Frequency` + falling CTR + rising `CPM`. Check before recommending a budget change.
- Break out `publisher_platform` and `platform_position` — CBO budget drifts to the cheapest
  placement, not the best one.
- The three ranking diagnostics separate a creative problem (quality/engagement) from an offer or
  landing page problem (conversion rate).
- Funnel: link clicks → `landing_page_views` → add to cart → purchase. A gap at the first step is
  usually site speed or tracking, not media.
- Compare with `compare_type="prev_range"` or `"prev_year"`.
- Flag: incomplete final day, iOS/SKAdNetwork under-reporting, learning phase, conversions still in
  the attribution window.

## Common requests

- **"How are we doing?"** → `adcampaign_name` + `cost` `impressions` `Clicks` `action_link_click`
  `offsite_conversions_fb_pixel_purchase` `website_purchase_roas`, with `compare_type="prev_range"`.
  State the `conversion_window` you used.
- **"Facebook or Instagram?"** → `publisher_platform` (+ `platform_position` to go deeper) with
  `cost` `impressions` `action_link_click` and your conversion metric. Often the single most
  revealing Meta query.
- **"Is the creative tired?"** → `ad_name` + `Frequency` `CPM` `unique_link_CTR` broken out by `Date`.
  Frequency up, CTR down, CPM up = replace the creative, do not touch the bid.
- **"Which ads are working?"** → `ad_name` + `cost` `website_purchase_roas` `quality_score_organic`
  `quality_score_ectr` `quality_score_ecvr`. The rankings say whether it is the creative or the offer.
- **"Where do we lose people?"** → `action_link_click` `landing_page_views`
  `offsite_conversions_fb_pixel_purchase` on one row. A gap at the first step is site speed or
  tracking, not media.
- **"Boost this post"** → find it with `campaign_and_resource_get(resource_type="posts")`, then
  `manage_campaign` with `object_story_id` = `{page_id}_{post_id}`. Created paused; confirm budget first.

## Querying

`filters` — `Clicks > 5`, `country == US AND clicks > 100`. **Operators need a space either side**;
`Clicks>5` and an HTML-escaped `&gt;` are both rejected. Ops: `==` `!=` `>` `>=` `<` `<=` `=@` `!@`
`=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs — `cost` renders as "Cost", `Clicks` as
  "Clicks (all)". Map by `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric. Report what actually came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.
- A `currency_recommendation` means monetary columns carry no currency — add `currency`.

## Write-back

Supermetrics writes to Meta. Offer it rather than sending the user to Ads Manager.

Read first with `campaign_and_resource_get(ds_id="FA", account_id=…)`. Never update what you haven't read.

`manage_campaign(ds_id="FA", account_id=…)`:

- **Create** — omit `campaign_id`; `budget_amount` required; set `platform_settings.objective`
  (`OUTCOME_TRAFFIC`|`OUTCOME_ENGAGEMENT`|`OUTCOME_LEADS`|`OUTCOME_SALES`|`OUTCOME_AWARENESS`) and
  `campaign_budget_optimization` — `true` puts budget on the campaign (CBO), `false` on each ad set
  (ABO). That decides where `budget_amount` belongs.
- **Ad sets** are `ad_groups[]`; their `platform_settings` carry `billing_event`,
  `optimization_goal`, `page_id`, `pixel_id`, `promoted_object`, `destination_type`, and that ad
  set's own `start_date`/`end_date`.
- **Every ad needs `page_id`** in the ad's `platform_settings` — the most common creation failure.
- **Creative** — `headlines[0]` = title, `descriptions[0]` = message. Carousels use `carousel_cards`
  (2–10), not `assets`. Video needs `asset_url` or `asset_id` with `asset_type: "video"`. To boost a
  post set `object_story_id` = `{page_id}_{post_id}` (IDs via `resource_type="posts"`); CTA and URL
  are inherited. `creative_picker` when there's no asset yet.
- **Update** — pass `campaign_id`; only sent fields change. Objective is fixed at creation; ABO
  campaigns can't switch bidding strategy.
- **`targeting` replaces, never merges.** Send the full set. `advantage_audience` toggles Advantage+.

Safety:

- Write access is enabled per account at
  [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings) with an approval
  level. Permission errors are almost always this.
- **New campaigns are always created paused.** `status: "ENABLED"` only on explicit approval of that
  campaign — it starts spend immediately.
- Confirm budget in writing first: amount, currency, daily vs lifetime, campaign vs ad set.
- Check `write_status` and `failures`; partial success happens, and blind retries duplicate.

Logged at [hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).
Re-read after writing; state what changed and what is still paused.

**Planning** (read-only): `reach_estimate`, `forecast`, `targeting_search`, `audiences`, `pages`,
`posts`, `recommendations`, `assets`, `conversion_types`, `history`.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-type conflict; the message names no field. Compare `report_types`, split the query. Not an account or date problem |
| `[FIELD_NOT_FOUND]` suggesting the same field back | Account-scoped custom conversion — re-run `field_discovery` against the account you're querying |
| Account not found / no access | `accounts_discovery(ds_id="FA")` |
| Auth error | `data_source_discovery(ds_id="FA")`, surface `login_link` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with Ads Manager | `supermetrics_guide(mode="data_discrepancies")` — check `conversion_window` first |
| Missing pages or ad accounts | `supermetrics_guide(mode="meta_instagram")` |
| Write rejected | `supermetrics_guide(mode="write_access")` |

## Docs

[Fields](https://docs.supermetrics.com/docs/facebook-ads-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-facebook-ads) ·
[Attribution windows](https://docs.supermetrics.com/docs/how-to-set-a-custom-conversion-window-in-facebook-ads-queries) ·
[Custom conversions](https://docs.supermetrics.com/docs/about-facebook-ads-custom-conversions) ·
[Campaign management](https://docs.supermetrics.com/docs/how-to-manage-ad-campaigns-with-ai-tools)
