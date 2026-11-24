---
name: supermetrics-get-started
description: >-
  Onboards a new Supermetrics user: lists connected data sources, hands over login links to
  connect Google Ads, Meta Ads, GA4, Instagram, LinkedIn, TikTok, Shopify, HubSpot and other
  platforms, then takes the user to a first real result in analysis, campaign management, social
  publishing or a live Supermetrics Studio dashboard, and saves their goals as business context.
  Also recovers from sign-in, license, trial and "source not connected" errors. Use when the user
  asks "how do I get started with Supermetrics", "what can I do with Supermetrics", "connect my
  Google Ads account", or "why does Supermetrics say I'm not signed in". Don't use for building
  specific data queries once sources are connected (use supermetrics-marketing-data-queries).
---

# Get started with Supermetrics

Get the user from install to a first real result in as few turns as possible. Do each step only if the previous one succeeded. Don't run every step for a user who arrives with a concrete request: connect what that request needs, and go straight to the matching path in step 2.

## 1. See what's connected, and ask what they came for

Call `data_source_discovery` with `compress: true`; `authentication_status` shows what's connected.

- **Tool missing or auth error:** the Supermetrics app isn't connected or signed in. Ask the user to connect or reconnect Supermetrics in ChatGPT's app settings and sign in with their Supermetrics account, then wait. Never ask for an API key or password in chat.
- **License or trial error (here or later):** don't loop on sign-in. Say so plainly and follow the note in the response; `supermetrics_guide` with `mode: license_and_trials` has the details.

On success, summarise in one short list grouped as **ads**, **analytics & sales**, and **social**. Don't list unconnected sources.

Then offer the four directions in one line each, marking the ones their connections already allow:

1. **Analyse performance** — spend, conversions, ROAS, traffic, across channels
2. **Manage campaigns** — budgets, bids, pausing, targeting, new campaigns
3. **Publish social posts** — post, schedule and reply to comments on Instagram, LinkedIn pages, TikTok and Google Business Profile
4. **Live dashboard** — a shareable Supermetrics Studio page that stays up to date

If they pick a direction whose platform isn't connected, call `get_user_and_team` with `action: login_data_source` and the `ds_id` (add `include_management_scopes: true` for social publishing and campaign management), hand over the link and wait. Never describe clicking through menus. For connection trouble, `supermetrics_guide` with `mode: connect_data_source`.

## 2. First result

### Analyse performance
Run one small report straight away: last 7 days vs the previous 7 for spend, clicks, conversions and cost per conversion (sessions and conversions for GA4; revenue and orders for Shopify). Show a compact table or chart, and call out the one change that matters most. Follow the `supermetrics-marketing-data-queries` skill for discovery and field IDs.

### Manage campaigns
1. List campaigns with `campaign_and_resource_get` for the chosen account, with last-7-day spend from `data_query`.
2. Check `campaign_and_resource_get` with `resource_type: write_readiness`. If write access is off, say a team admin turns it on in Supermetrics; never try to change it yourself. Reading still works meanwhile.
3. Offer one concrete, low-risk change based on the data (pause a zero-conversion campaign, shift budget to the best CPA). Follow the `supermetrics-campaign-management` skill before any write: read first, confirm with the user, check `write_status`.

### Publish social posts
1. `accounts_discovery` for the platform (`IGI`, `LIP`, `TIKBA` or `GMB`); confirm the account if there are several.
2. Show what's already scheduled with `manage_social_media` `action: status`, and the last few posts' engagement from `data_query` on the same `ds_id`.
3. Offer to draft a post or reply to recent comments. Follow the `supermetrics-social-media-publishing` skill: the user approves the exact post, and a `validate_only: true` dry run comes before every publish.

### Live dashboard
Ask which question it should answer (weekly paid media, budget pacing, social growth). Prove the data with one `data_query`, show a static preview in chat, and on a yes publish with `manage_dashboards`. Follow the `supermetrics-live-dashboards` skill. Give the link and one sentence on how to use it.

## 3. Save their goals

After the first result, ask once what they're working towards (target CPA or ROAS, monthly budget, posting frequency, follower goal). Save it with `manage_business_context` (`action: save`, `context_text` with the goal, `tags` such as `targets` or `brand-language`) so later work is judged against it. Skip if context already exists.

## 4. Point to what's next

Suggest three follow-ups that cross into the other directions, based on what they connected, for example: after an analysis, "pause the campaigns wasting spend" and "turn this into a live dashboard"; after a social post, "which posts drove the most engagement last month"; after a campaign change, "a pacing dashboard to watch the effect".

If the user asks about an earlier support request, `get_support_tickets` lists their own tickets and reads one by `ticket_id`. To invite a colleague to the team, use `manage_user_and_team` with `action: invite_member`, `email` and `role`; the invite is sent only after the user approves it on the link the tool returns.
