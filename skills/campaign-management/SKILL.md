---
name: campaign-management
description: Create, update, pause and audit advertising campaigns through the Supermetrics MCP server across Google Ads, Meta Ads, Microsoft Advertising, TikTok Ads, LinkedIn Ads and ChatGPT Ads. Use when the user wants to launch a campaign, change a budget or bid, pause or enable ads, edit targeting, manage creatives, or review what changed.
---

# Campaign management with Supermetrics

These tools spend real money. Treat every write as consequential.

## Before writing anything

1. Campaign writes must be enabled per advertising account at
   [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings).
   If a write fails with a permissions error, that is usually why.
2. Read before you write. `campaign_and_resource_get` shows current state — campaigns,
   ad groups, ads, keywords, audiences, budgets. Never update a campaign you have not read.
3. Check business context with `manage_business_context` (`action="get"`, plus `ds_id`
   and `account_id`). Naming conventions, budget ceilings and excluded campaigns live there.

## Safety rules

- **New campaigns are always created paused.** This is enforced by the platform layer;
  do not try to work around it.
- **Only set `status: "ENABLED"` when the user has explicitly approved that specific
  campaign going live.** Enabling starts spend immediately.
- **Google Performance Max**: enabling the campaign is not enough — its asset groups are
  paused too. Enabling those is a second, separately approved step.
- **`targeting` replaces, it does not merge.** Send the complete set every time, or you
  will silently delete targeting the user wanted to keep. The exception is Google and
  Microsoft keywords, where `add_keywords` / `remove_keywords` append and remove without
  touching the rest.
- **Confirm budgets in writing** before submitting. State the amount, the currency and
  whether it is daily or lifetime.

## Creating a campaign

Use `manage_campaign` without a `campaign_id`. `budget_amount` is required. Structure:

- Campaign level: name, budget, bidding strategy, dates, campaign-level targeting.
- `ad_groups[]`: ad sets or ad groups. Omit `id` to create, include `id` to update.
- `ad_groups[].ads[]`: each needs a name and a `creative` (headlines, descriptions,
  `final_urls`). Meta ads additionally require `page_id` in the ad's `platform_settings`.

Creatives accept, in order of preference: `asset_url` (a public URL), `asset_id` (already
in the ad account — find them via `campaign_and_resource_get` with
`resource_type="assets"`), or `upload_ref` from `resources_manage`. If the user has no
creative yet, open `resources_manage` to browse, upload or generate one.

Platform quirks worth knowing before you build the payload:

- **Google Ads**: 3–15 headlines (30 chars), 2–4 descriptions (90 chars), `final_urls`
  required. Performance Max images must be 1.91:1, 1:1 or 4:5 — 16:9 and 9:16 are rejected.
- **Meta**: budget sits on the campaign under CBO, on each ad set under ABO. Carousels use
  `carousel_cards`, not `assets`.
- **TikTok**: video creatives must use `asset_id`; a URL will not work.
- **LinkedIn**: single image only.

## Updating

Pass `campaign_id` to update. Only the fields you send change. Campaign type and objective
are fixed at creation and cannot be changed afterwards.

Partial success is possible on multi-entity writes. Always check `write_status` and
`failures` in the response — do not assume success and do not blindly retry, which can
create duplicates.

## After writing

Every change is logged at
[hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).
Re-read with `campaign_and_resource_get` to confirm the final state, and tell the user
exactly what was created or changed, including which entities are paused.

## Forecasting and research

Before committing spend, `campaign_and_resource_get` also offers `resource_type` values
for planning: `forecast` (estimated performance at a given budget), `keyword_ideas` and
`keyword_volumes`, `reach_estimate`, `targeting_search`, `audiences` and `recommendations`.
