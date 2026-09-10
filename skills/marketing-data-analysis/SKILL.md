---
name: marketing-data-analysis
description: Query and analyze live marketing performance data through the Supermetrics MCP server. Use when the user asks about ad spend, ROAS, CPA, campaign performance, website traffic, conversions, channel comparisons, or any metric from Google Ads, Meta Ads, GA4, LinkedIn Ads, TikTok Ads, Microsoft Advertising, Shopify, HubSpot or any other connected marketing platform.
---

# Marketing data analysis with Supermetrics

Never answer a performance question from memory. Supermetrics returns live data — query it.

## The workflow

Follow these steps in order. Skipping discovery is the most common cause of failed queries.

1. **`data_source_discovery()`** — list sources and their auth status. Pick the one the
   user means. If they say "Facebook" or "Meta", that is `FA`; "Google Ads" is `AW`;
   "GA4" is `GAWA`.
2. **`data_source_discovery(ds_id=X)`** — read that source's configuration before
   querying. It tells you what the next steps must be:
   - `has_account_list: true` → call `accounts_discovery(ds_id=X)` for account IDs.
   - `has_report_type_selection: true` → choose a `report_type` and pass it in `settings`.
   - `has_fields: true` → call `field_discovery(ds_id=X)` to get valid field IDs.
   - `is_date_range_required: true` → set a date range (nearly always true).
3. **`field_discovery(ds_id=X, filter="...")`** — filter rather than dumping every field.
   `filter="cost,click,conversion"` is far more useful than the full catalogue.
4. **`data_query(...)`** — dimensions before metrics in `fields`. All fields must belong
   to the same report type.
5. **`get_async_query_results(schedule_id=...)`** — if the query returns a schedule ID
   instead of rows, poll immediately and keep polling until `completed`.

## Business context

Before analyzing a specific account, call `manage_business_context` with `action="get"`
and the `ds_id` (plus `account_id` where relevant). Teams store reporting conventions
there — which campaigns to exclude, which attribution window to use, how to segment.
Applying them is the difference between a correct answer and a plausible one.

If the user states a durable preference mid-conversation ("always exclude brand
campaigns", "we report in EUR"), offer to save it with `action="save"`.

## Writing good queries

- **Dates**: prefer relative ranges (`last_30_days`, `last_month`, `this_month_inc`).
  For anything relative to "now", call `get_today` first — do not assume the date.
- **Comparisons**: `compare_type="prev_period"` or `"prev_year"` answers "how does that
  compare?" in one query instead of two.
- **Filters**: `filters="country == US AND clicks > 100"`. Operators include `==`, `!=`,
  `>`, `>=`, `=@` (contains), `=~` (regex), `[]` (in list).
- **Row limits**: default is 1000. Raise `max_rows` for long date ranges broken out by day.

## Reading results

Rows come back as a 2D array: row 0 is display headers, rows 1+ are data. Map columns by
`requested_field_ids`, **not** by the row-0 labels — the labels are human-readable names
that differ from field IDs (`screenPageViews` displays as "Views"), so matching on labels
silently reads the wrong column.

Values may be strings. Parse numbers before arithmetic.

## Interpreting, not just reporting

A table is not an answer. After retrieving data:

- Lead with what changed and why it matters, not with the numbers.
- Compare against a baseline — prior period, prior year, or the account average.
- Flag anomalies: sudden CPA moves, campaigns with spend but no conversions, sources
  whose data ends before the requested range (often a broken connection, not a real zero).
- Say when a number is unreliable — GA4 sampling, incomplete last day, thresholding.

## When something fails

Call `supermetrics_guide` with the matching `mode` before escalating: `no_data`,
`data_discrepancies`, `ga4_data`, `query_errors`, `reconnect`, `prioritized_accounts`,
`quotas_and_limits`. Most failures are configuration, not bugs, and the guide resolves
them without a support ticket.
