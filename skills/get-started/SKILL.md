---
name: get-started
description: First-run onboarding for Supermetrics. Use right after the user connects Supermetrics, when they ask how to get started or what data they can access, when they want to connect Google Ads, Meta, GA4, LinkedIn or another platform, or when a Supermetrics call fails because they aren't signed in or a source isn't connected.
---

# Get started with Supermetrics

Take the user from install to their first real answer in as few turns as possible. Do each step only if the previous one succeeded.

## 1. Check sign-in

Call `get_user_and_team` with `action: get_info`.

- **Tool not available or auth error:** the Supermetrics connector isn't connected or signed in. Ask the user to connect or reconnect Supermetrics in their AI app's connector or app settings and sign in with their Supermetrics account (new users get a 14-day free trial, no card). Then stop and wait. Never ask for an API key or password in chat.
- **Signed in, but `license` holds an error, or a tool returns a license or trial error:** don't loop on sign-in. Say so plainly and follow the note in the response; buying is at https://supermetrics.com/pricing, and the plan must include the AI Chats destination. For details, call `supermetrics_guide` with `mode: license_and_trials`.
- **Success:** greet them by name and team in one line.

## 2. See what's connected

Call `data_source_discovery` with `compress: true`; each source's `authentication_status` shows what's connected. Then call `accounts_discovery` for the connected platforms the user cares about. Summarise in one short list: connected platforms and account counts. Don't dump every source.

If nothing relevant is connected, ask which platform they want first (Google Ads, Meta Ads, GA4, LinkedIn Ads, TikTok Ads, Microsoft Ads, Shopify, HubSpot…) and call `get_user_and_team` with `action: login_data_source` and its `ds_id`. Give them the login link and wait. Never describe clicking through menus; hand over the link. For platform-specific connection help, call `supermetrics_guide` with `mode: connect_data_source`.

## 3. First answer

Once at least one account is connected, run one small useful report straight away — last 7 days vs the previous 7 for spend, clicks, conversions and cost per conversion (or sessions and conversions for GA4) — and show it as a compact chart or table.

## 4. Save their goals

Ask what they're optimizing for (target CPA, ROAS or monthly budget). Save it with `manage_business_context` (`action: save`, tags like `targets`) so later analyses judge results against it. Skip this if a profile already exists.

## 5. Point to what's next

Suggest three questions tailored to what they connected, for example a wasted-spend audit, a weekly performance review, budget pacing, or launching a campaign. Mention that results can be published as a live Supermetrics Studio dashboard.
