# Supermetrics

This project has the Supermetrics MCP server available. It provides live access to
marketing, advertising, analytics and e-commerce platforms — Google Ads, Meta Ads,
Google Analytics 4, LinkedIn Ads, TikTok Ads, Microsoft Advertising, Shopify, HubSpot,
Amazon Ads, Klaviyo, Salesforce and more.

## Use it for

- Marketing performance questions — spend, ROAS, CPA, conversions, traffic, engagement.
- Cross-channel comparisons and period-over-period analysis.
- Reading and writing advertising campaigns on Google, Meta, Microsoft, TikTok,
  LinkedIn, ChatGPT and Snapchat Ads.
- Building shareable live dashboards in Supermetrics Studio.

Never answer a performance question from memory. Query the data.

## Tools

- Data: `data_source_discovery`, `accounts_discovery`, `field_discovery`, `data_query`,
  `get_async_query_results`, `get_today`, `instagram_insights`.
- Campaigns: `campaign_and_resource_get`, `manage_campaign`, `creative_picker`.
- Audits and monitors: `manage_audits_and_monitors`, `read_audits_and_monitors`.
- Organic social: `manage_social_media`.
- Dashboards: `get_dashboards`, `manage_dashboards`.
- Business context: `get_business_context`, `manage_business_context`.
- Account and team: `get_user_and_team`, `manage_user_and_team`.
- Help and utilities: `supermetrics_guide`, `marketing_utilities`, `get_support_tickets`,
  `contact_supermetrics`.

## Workflow

1. `data_source_discovery()` — list sources, pick one by `ds_id` (`AW` Google Ads,
   `FA` Meta Ads, `GAWA` GA4).
2. `data_source_discovery(ds_id=X)` — read its config. It tells you whether you need
   accounts, a report type, fields, or a date range.
3. `accounts_discovery(ds_id=X)` — when `has_account_list` is true.
4. `field_discovery(ds_id=X, filter="cost,click")` — filter; don't dump the catalogue.
5. `data_query(...)` — dimensions before metrics; all fields from one report type.
6. `get_async_query_results(schedule_id=...)` — poll if the query returned a schedule ID.

Pass `compress: true` to get results as a compact text table whose columns are the requested
field IDs. Uncompressed, results are a 2D array: row 0 is display headers, rows 1+ are data. Map
columns by `requested_field_ids`, not by the row-0 labels — they differ. A query can end
`status: "failed"`: read `error` and call `supermetrics_guide(mode="query_errors")`.

## Business context

Teams store reporting conventions — excluded campaigns, attribution preferences, currency,
segmentation — as business context. Read it with `get_business_context` before analyzing an
account; offer to save durable preferences the user states with `manage_business_context`.

## Campaign writes

Writes must be enabled per account at hub.supermetrics.com/write-settings. New campaigns
are always created paused — only enable one on explicit approval. On update, omitted
`targeting` fields are kept; a field you send replaces that whole list, so send its complete value. Every change is logged at
hub.supermetrics.com/campaign-history.

## When something fails

Call `supermetrics_guide` with a troubleshooting mode — `no_data`, `data_discrepancies`,
`ga4_data`, `query_errors`, `reconnect`, `quotas_and_limits` — before escalating.
