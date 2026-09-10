# Supermetrics

This project has the Supermetrics MCP server available. It provides live access to 174
marketing, advertising, analytics and e-commerce platforms — Google Ads, Meta Ads,
Google Analytics 4, LinkedIn Ads, TikTok Ads, Microsoft Advertising, Shopify, HubSpot,
Amazon Ads, Klaviyo, Salesforce and more.

## Use it for

- Marketing performance questions — spend, ROAS, CPA, conversions, traffic, engagement.
- Cross-channel comparisons and period-over-period analysis.
- Reading and writing advertising campaigns on Google, Meta, Microsoft, TikTok,
  LinkedIn and ChatGPT Ads.
- Building shareable live dashboards in Supermetrics Studio.

Never answer a performance question from memory. Query the data.

## Workflow

1. `data_source_discovery()` — list sources, pick one by `ds_id` (`AW` Google Ads,
   `FA` Meta Ads, `GAWA` GA4).
2. `data_source_discovery(ds_id=X)` — read its config. It tells you whether you need
   accounts, a report type, fields, or a date range.
3. `accounts_discovery(ds_id=X)` — when `has_account_list` is true.
4. `field_discovery(ds_id=X, filter="cost,click")` — filter; don't dump the catalogue.
5. `data_query(...)` — dimensions before metrics; all fields from one report type.
6. `get_async_query_results(schedule_id=...)` — poll if the query returned a schedule ID.

Results are a 2D array: row 0 is display headers, rows 1+ are data. Map columns by
`requested_field_ids`, not by the row-0 labels — they differ.

## Business context

`manage_business_context` stores a team's reporting conventions — excluded campaigns,
attribution preferences, currency, segmentation. Read it before analyzing an account;
offer to save durable preferences the user states.

## Campaign writes

Writes must be enabled per account at hub.supermetrics.com/write-settings. New campaigns
are always created paused — only enable one on explicit approval. `targeting` replaces
rather than merges, so always send the complete set. Every change is logged at
hub.supermetrics.com/campaign-history.

## When something fails

Call `supermetrics_guide` with a troubleshooting mode — `no_data`, `data_discrepancies`,
`ga4_data`, `query_errors`, `reconnect`, `quotas_and_limits` — before escalating.
