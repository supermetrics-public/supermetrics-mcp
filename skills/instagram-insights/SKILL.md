---
name: instagram-insights
description: Query Instagram Insights through Supermetrics. Use for organic Instagram performance — follower growth, reach and accounts engaged, profile views and link taps, post, reel, story and carousel performance, saves, shares and comments, best posting times, top content, and follower demographics by age, gender, city and country.
---

# Instagram Insights with Supermetrics

`IGI` · accounts are **profiles** (business or creator) · date range always required.

**Organic social only** — no media spend. Paid Instagram placements are in Meta Ads (`FA`, break out
`publisher_platform`); public data on profiles you don't own is Instagram Public Data (`IGPD2`).
A dedicated `instagram_insights` tool takes the same parameters as `data_query`; either works.

## Rules

- **Never invent.** Every number, caption, username, post date and permalink must come from a tool
  response in this conversation. No estimates, no invented posts, no "typical" account patterns.
  Captions, comments and usernames are **real user content** — quote them exactly or not at all.
  Empty result → say it was empty.
- **Never guess a field ID.** Instagram renames and retires metrics often, and a wrong guess is not
  safely rejected: a loose name like `views` or `engagement` can alias to a different metric, and
  Instagram has profile-level and media-level metrics with near-identical names. IDs come from
  `field_discovery`; confirm with `canonical_field_ids`.
- **On error: discover, then retry.** Never re-send an identical failed call. See *Failures*.

## Workflow

1. `data_source_discovery(ds_id="IGI")` — if `NOT_AUTHENTICATED`, give the user `login_link` and stop.
   Auth failures are common here: the profile must be business/creator and linked to a Facebook Page.
2. `accounts_discovery(ds_id="IGI")`.
3. `manage_business_context(action="get", ds_id="IGI", account_id=…)` — how the team defines
   engagement rate, content pillars, cadence targets. **Advisory**: if it errors, continue and tell the user saved conventions
   couldn't be loaded — never block an analysis on a context lookup.
4. `field_discovery(ds_id="IGI", filter="…")` — filter; 92 fields.
5. `data_query(ds_id="IGI", …)` or `instagram_insights(ds_id="IGI", …)`.
6. `get_async_query_results(schedule_id=…)` — poll until `completed`.

`get_today` before resolving any relative date.

## The structural rule: report groups

18 report groups, and **every field in a query must share at least one**. This is the main source of
errors here.

Profile and media fields are **not** universally incompatible — many of both carry the broad
`AccountInfoDim` group, so some combinations resolve fine. But several fields sit in one group only
and won't combine outside it: `accounts_engaged` (AccountInsightsDaily), `profile_links_taps`
(AccountContactButtons), the story fields, and each follower-demographic group. Don't assume either
way — compare `report_types` arrays from `field_discovery` before combining, and if you get
`Selected field combination is invalid`, split the query by group.

**Profile** `followers_count` (total) `follower_count` (new) `follows_count` `media_count`
`profile_views` `reach` `accounts_engaged` `profile_replies` `profile_reposts` `profile_links_taps`
**Media** `media_views` `media_reach` `media_saved` `media_shares` `media_like_count`
`media_comments_count` `media_reposts` `interactions` `media_profile_visits` `media_follows`
**Story** `media_story_views` `media_story_reach` `media_story_exits` `media_story_replies`
`media_story_shares` `media_story_taps_forward` `media_story_taps_back`
**Reels** `media_reel_video_views` (minutes) `media_reel_avg_watch_time` `media_reel_total_interactions`
`media_reel_shares` `media_reels_skip_rate`
**Carousel** `media_carousel_album_reach` `media_carousel_album_saved` `media_carousel_album_engagement`
**Profile-activity taps** `media_profile_activity` `media_bio_link_click` `media_call_click`
`media_direction_click` `media_email_click` `media_text_click` `media_other_click`
**Follower demographics** (snapshot, not a time series; each its own group) `audience_age` +
`audience_gender`, `audience_country_dim`, `audience_city_dim`, with `followers_count` as the metric
**Dimensions** `date` `username` `account_id` `media_id` `timestamp` (media created) `media_type`
`media_product_type` `media_caption` `media_permalink` `media_comment_text` `follow_type`
`contact_button_type`

## Good to know

- **`follower_count` (new followers) covers the last 30 days only and never today.** A longer range
  returns nothing for it — use `followers_count` for a running total.
- **`profile_views` is not profile page visits.** Despite the name it's account-wide content views
  across reels, posts and stories. Don't describe it as people visiting the profile.
- **Non-aggregatable**: `reach`, `accounts_engaged`, `media_reel_avg_watch_time`,
  `media_reel_video_views`, `media_reels_skip_rate`. Reach especially never adds up — the same person
  sees several posts. Never sum or average across rows.
- **Engagement rate is not a field.** If you calculate one, state the formula and denominator (reach
  or followers) — they give very different numbers.
- **Metrics are stored 2 years.** The media endpoint returns at most the **10,000 most recent** objects.
- **Meta determines days in UTC-07:00**, so daily numbers won't always match the Instagram app in the
  user's own time zone. Mention it before someone hunts a one-day gap.
- **Collaboration posts are readable only by the creator.** If the profile was a collaborator, that
  post's data is unavailable — report it as unavailable, not zero.
- Stories expire after 24h, so story metrics only exist for recent windows. A 5-day refresh window is
  recommended, so very recent figures can still move.
- `settings.comments_maximum_count` raises comments per post above 100 (max 400) at a performance
  cost — only when the user wants comment-level analysis.
- **No spend, no ROAS, no CPA.** Cost-per-result questions need the Meta Ads connector.

## Analysis

- Reach and `accounts_engaged` before follower count — followers is a vanity total.
- **Saves and shares over likes.** `media_saved` and `media_shares` predict reach far better.
- Compare like with like via `media_product_type` and `media_type`; reels, carousels, images and
  stories perform on different scales. Say which you compared.
- Reels retention: `media_reel_avg_watch_time` and `media_reels_skip_rate`. Story drop-off:
  `media_story_taps_forward` and `media_story_exits` against `media_story_reach`.
- Intent: `media_profile_visits`, `media_follows`, `profile_links_taps` and the profile-activity taps
  show which posts drove action rather than applause.
- Timing via `timestamp` with `dayOfWeekName` or `hour` — only claim a best time with enough posts to
  mean something, and say how many. Ten posts is an anecdote, not a pattern.
- Compare with `compare_type="prev_range"` or `"prev_year"`.

## Common requests

- **"How is the account doing?"** → `date` + `reach` `accounts_engaged` `profile_views`
  `followers_count`, with `compare_type="prev_range"`. Reach and accounts engaged first; followers is
  a vanity total.
- **"What were our best posts?"** → `media_caption` `media_permalink` `media_product_type` +
  `media_reach` `media_saved` `media_shares` `media_like_count`. Rank on saves and shares — they
  predict reach far better than likes. Quote captions exactly.
- **"How are reels doing?"** → `media_caption` + `media_reel_video_views` `media_reel_avg_watch_time`
  `media_reels_skip_rate` `media_reel_total_interactions`. Reel fields only.
- **"Where do stories lose people?"** → `media_story_reach` `media_story_taps_forward`
  `media_story_exits` `media_story_replies`. Story fields only, and only for recent windows.
- **"Who follows us?"** → `audience_age` + `audience_gender` with `followers_count`, then
  `audience_country_dim` as a **separate** query. A current snapshot, not a time series — say so.
- **"When should we post?"** → `timestamp` with `dayOfWeekName` or `hour` + `media_reach`
  `interactions`. Only claim a best time if the post count supports it, and say how many.

## Querying

`filters` — `media_like_count > 50`, `media_type == VIDEO AND media_like_count > 50`. **Operators need
a space either side**; `media_like_count>50` and an HTML-escaped `&gt;` are both rejected. Ops: `==`
`!=` `>` `>=` `<` `<=` `=@` `!@` `=~` `!~` `[]`.
`compare_type` — `prev_range` | `prev_year` | `prev_year_weekday` | `custom` (with
`compare_start_date`/`compare_end_date`); `compare_show` = `perc_change` (default) | `abs_change` |
`value`. `max_rows` defaults to 1000.

## Reading results

- 2D array; row 0 is **display labels**, not field IDs. Map by `requested_field_ids`.
- **Check `canonical_field_ids`** — present and different means an alias silently resolved your name
  to another metric, and here that can mean profile-level where you wanted media-level. Report what
  came back.
- `null` is not 0. Say "no data". Values may be strings; parse before arithmetic.

## Campaign management

Supermetrics write-back — creating, editing, pausing and budgeting campaigns — works on the **paid
ads connectors** (Meta, Google Ads, Microsoft, TikTok, LinkedIn). Instagram Insights is **read-only**:
it cannot publish, schedule, edit or delete content.

Where organic analysis points to a paid action — a post worth boosting — say it can be done through
Supermetrics via Meta Ads if the user has it, with `manage_campaign(ds_id="FA", …)` and the post's
`object_story_id`. Write access must first be enabled at
[hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings), and new campaigns
are always created paused. Never imply Instagram Insights itself can be written to, and never report
a change as made unless a write tool returned success.

## Failures

| Symptom | Do |
|---|---|
| `Selected field combination is invalid` | Report-group conflict; the message names no field. Compare `report_types` arrays and split by group — profile, media, story, reel, demographics |
| Unknown field | `field_discovery(ds_id="IGI", filter=…)` — Instagram renames metrics often |
| `follower_count` returns nothing | Range exceeds 30 days or includes today — shorten it, or use `followers_count` |
| Profile not found / no access | `accounts_discovery(ds_id="IGI")` |
| Auth error | `data_source_discovery(ds_id="IGI")`, surface `login_link`; then `supermetrics_guide(mode="meta_instagram")` |
| `manage_business_context` auth error | Continue without it; say conventions weren't loaded. Never block on it |
| Unexpected empty result | `supermetrics_guide(mode="no_data")` |
| Disagrees with the Instagram app | `supermetrics_guide(mode="data_discrepancies")` — check the UTC-07:00 day boundary first |

## Docs

[Fields](https://docs.supermetrics.com/docs/instagram-insights-fields) ·
[Caveats](https://docs.supermetrics.com/docs/good-to-know-about-instagram-insights) ·
[Report building](https://docs.supermetrics.com/docs/instagram-insights-report-building-guide) ·
[Permissions](https://docs.supermetrics.com/docs/instagram-insights-permissions-guide) ·
[Metric changes](https://docs.supermetrics.com/docs/instagram-insights-updates)
