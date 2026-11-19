---
name: supermetrics-get-started
description: >-
  Onboards a new Supermetrics user: lists connected data sources and accounts, suggests what to
  connect first (ad platforms where Supermetrics can also run campaigns, social accounts it can
  post to, or analytics and e-commerce sources), hands over login links, runs a first 7-day
  performance snapshot shown as a chart, offers a first hands-on action such as a budget change or
  a scheduled post, offers a live Studio dashboard and saves the user's targets (CPA, ROAS,
  budget) as business context. Also recovers from sign-in, license, trial and "source not
  connected" errors. Use when the user asks "how do I get started with Supermetrics", "what can
  you do", "connect my Google Ads account", or "why does Supermetrics say I'm not signed in".
  Don't use for building specific data queries once sources are connected (use
  supermetrics-marketing-data-queries).
---

# Get started with Supermetrics

Take the user from install to their first real answer, and their first real action, in as few turns as possible. New users already have a free trial, so start with their data, not with account checks. Do each step only if the previous one succeeded.

## 1. See what's connected

Call `data_source_discovery` with `compress: true`; each source's `authentication_status` shows what's connected. Then call `accounts_discovery` for the connected platforms the user cares about. Summarise in one short list: connected platforms and account counts. Don't dump every source.

## 2. Suggest what to connect

If nothing relevant is connected, or the user asks what else is possible, ask which platform they want first. Offer the choices in three short groups, so they see that Supermetrics can act as well as report:

- **Ad platforms — report and manage campaigns** (create, pause, change budgets and targeting): Google Ads, Meta Ads (Facebook and Instagram ads), Microsoft Advertising, LinkedIn Ads, TikTok Ads, Snapchat Ads, ChatGPT Ads.
- **Organic social — report, post and reply to comments**: Instagram, LinkedIn company pages, TikTok, Google Business Profile.
- **Analytics, e-commerce and CRM — report**: GA4, Search Console, Shopify, HubSpot and 200+ more.

When they pick one, call `get_user_and_team` with `action: login_data_source` and its `ds_id`. Give them the login link and wait. Never describe clicking through menus; hand over the link. For platform-specific connection help, call `supermetrics_guide` with `mode: connect_data_source`.

## 3. First answer

Once at least one account is connected, run one small useful report straight away: last 7 days vs the previous 7 for spend, clicks, conversions and cost per conversion (sessions and conversions for GA4; reach and engagement for an organic social account).

If your app can draw charts or render visuals, show the result as a compact chart with the key numbers beside it; otherwise use a short table. Add one or two sentences on what stands out.

## 4. First action (ad and social platforms only)

If the connected platform is one Supermetrics can manage, offer one concrete, low-risk action drawn from what the report just showed. Ask before doing anything; never act on your own initiative.

- **Ad platform:** for example pause the ad group with spend and no conversions, move budget toward the best campaign, add negative keywords, or draft a new campaign (new campaigns are always created paused). Load the `supermetrics-campaign-management` skill before any write and follow its rules.
- **Organic social:** for example draft a post for the user to approve and schedule it, or reply to recent comments. Load the `supermetrics-social-media-publishing` skill before any write and follow its rules.

If a write is refused with `WRITE_ACCESS_NOT_ENABLED`, writes aren't switched on for that account yet: give the user the link in the error (it opens the account's write settings) and explain that they choose there how much needs their approval. Then carry on with the next step.

## 5. Make it a dashboard

Offer to turn the snapshot into a fuller, polished live dashboard in Supermetrics Studio: a shareable page that refreshes with current numbers every time it opens, and can include campaign actions such as pausing a campaign. If they say yes, load the `supermetrics-live-dashboards` skill and follow it. Offer once; don't push if declined.

## 6. Save their goals

Ask what they're optimizing for (target CPA, ROAS or monthly budget). Save it with `manage_business_context` (`action: save`, `context_text` with the goal, `tags` like `targets`) so later analyses judge results against it. Skip this if a profile already exists.

## 7. Point to what's next

Suggest three questions tailored to what they connected, for example a wasted-spend audit, a weekly performance review, budget pacing, launching a campaign or planning a week of posts.

## If something goes wrong

- **Supermetrics tools missing, or an auth error on the first call:** the connector isn't connected or signed in. Ask the user to connect or reconnect Supermetrics in their AI app's connector or app settings and sign in with their Supermetrics account (new users get a 14-day free trial, no card). Then stop and wait. Never ask for an API key or password in chat.
- **A license or trial error:** don't loop on sign-in. Say so plainly and follow the note in the response; buying is at https://supermetrics.com/pricing, and the plan must include the AI Chats destination. For details, call `supermetrics_guide` with `mode: license_and_trials`. `get_user_and_team` with `action: get_info` shows the user's license and team.
- **Other help:** if the user asks about an earlier support request, `get_support_tickets` lists their own tickets and reads one by `ticket_id`. To invite a colleague to the team, use `manage_user_and_team` with `action: invite_member`, `email` and `role`; the invite is sent only after the user approves it on the link the tool returns.
