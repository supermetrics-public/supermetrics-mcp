---
name: live-dashboards
description: Plan, build and publish live, shareable marketing dashboards in Supermetrics Studio with manage_dashboards — pages that re-query the user's data on every view and can include campaign management actions. Use when the user asks for a dashboard, a report the team can reopen, a shareable link, or a recurring view (weekly review, pacing, KPIs), and to edit or find existing Studio dashboards.
---

# Live dashboards in Supermetrics Studio

A Studio dashboard is a page stored in Supermetrics Studio that fetches the user's data every time someone opens it, so a shared link always shows current numbers. Dashboards aren't limited to reading data: they can also include campaign management, such as pausing or enabling a campaign or changing a budget straight from the page. The `manage_dashboards` tool uploads, reads, edits and lists them.

**The `manage_dashboards` tool description and its parameter descriptions are the technical source of truth.** Follow them exactly for the code: how to call `smQuery`, how to parse its response, region markers, query keys, date and comparison parameters, `allowed_tools`, `allowed_accounts`, patch types and BigQuery. This skill covers when to offer a dashboard, how to plan it, and what makes it useful.

## When to use

- The user asks for a dashboard, a live report or a link to share.
- An analysis will be reopened: a weekly performance review, a daily check, budget pacing, a monthly SEO or channel review, a client report. Offer a live dashboard once at the end of that analysis; don't push it again if declined.
- The user wants to change, find or see the history of a dashboard they already have.

A one-off question needs a table or chart in the chat, not a dashboard.

## Workflow: new dashboard

### 1. Agree what it's for
Settle this in one short exchange at most, using what the conversation already tells you:
- **Question it answers** — "are we on budget this month?", "how did paid media do this week?", "which campaigns need attention?". One dashboard, one main question.
- **Audience** — the user, their team, leadership or a client. Leadership wants few numbers and trends; a media buyer wants campaign-level detail.
- **Sources and accounts** — which data sources and account IDs. Use `data_source_discovery` and `accounts_discovery` if not known.
- **Controls** — a date range by default; account, channel or campaign selectors and a period comparison if the audience will use them.
- **Actions** — whether viewers should be able to act on what they see, for example pause a campaign from the table or adjust a budget next to its pacing figure.

### 2. Prove the data first
Run each query once with `data_query` and `get_async_query_results`, using exact field IDs from `field_discovery`. Fix wrong fields, missing accounts or connection errors now; a broken query inside a published dashboard only shows an error to every viewer. The results also feed the preview.

### 3. Show a static preview
Build the dashboard with the numbers from step 2 written in and show it in the chat. Keep it close to what will be published, so the user is approving the real layout.

### 4. Confirm, then publish
Ask whether to publish it as a live Studio dashboard, unless the user already asked for a link. Then rebuild the data layer for Studio as the tool instructions describe and upload. Never upload the static preview.

### 5. Hand it over
Check the upload response and act on anything the tool instructions say to fix before sharing. Then give the user the link and one sentence on how to use it ("updates on every view; change the date range at the top").

## Workflow: change an existing dashboard

1. Find it with `list` (filter by a word from the title) unless you have its ID.
2. `get` the current code, then `edit` with the smallest patch that does the job, as the tool instructions describe. Add a short `change_summary`.
3. Use `changelog` when the user asks what changed or when.

## Campaign management in dashboards

A dashboard can act as well as report: a campaign table with pause and enable buttons, a pacing view where budgets can be adjusted, or a wasted-spend view with a one-click pause for the worst offenders.

- Add actions when the user asks for them or when the dashboard's purpose is to act (pacing, wasted spend, daily checks). Read-only is the default for leadership and client dashboards.
- Include the campaign tools the page calls in `allowed_tools` (for example `campaign_and_resource_get` and `manage_campaign`), and only the accounts it manages in `allowed_accounts`, as the tool instructions describe.
- Follow the `campaign-management` skill for the write itself: read the current state first, send only the fields that change, and check `write_status` in the result.
- Make every action deliberate: name the campaign and the exact change in a confirmation step before it runs, then show the result or the error in the page.
- The account's write settings and approval rules still apply. If a change needs approval, show that it is waiting instead of reporting it as done.
- Viewers act through the dashboard owner's connections, so tell the user that anyone they share an action dashboard with can make those changes, and suggest sharing it only with the people who should.

## What makes a good marketing dashboard

**Order: headline, trend, detail.** Three to six KPI cards on top, one or two trend charts below, then a table for drill-down. Someone who only reads the top row should still get the answer.

**Pick KPIs for the question:**
- Paid media performance — spend, conversions, cost per conversion, ROAS or conversion value, CTR.
- Budget pacing — month-to-date spend against budget, projected month-end spend, daily run rate, by channel.
- Ecommerce — revenue, orders, blended ROAS, average order value, new vs returning customers.
- Lead generation and B2B — leads, cost per lead, MQLs or pipeline by channel.
- SEO and content — clicks, impressions, average position and CTR from Search Console; sessions and conversions by landing page.
- Leadership summary — a handful of business numbers against the previous period or target, no campaign tables.

**Compare, don't just report.** A number is meaningful next to the previous period, the same period last year or a target. Add a period comparison when the audience asks "is this good?".

**Show direction by business meaning.** Green for good, red for bad: falling CPA is green even though the number went down.

**Use the account currency and clear formatting** — currency symbols, thousands separators, percentages with one decimal.

**Make the tables useful.** Sort by what matters (spend or conversions), show the top rows, and include the efficiency column (CPA, ROAS) next to the volume columns.

**Keep cross-channel numbers honest.** When combining platforms, use the same metric definitions and say where platform-reported conversions differ from GA4 or the CRM.

**Name it so it's findable later** — what, for whom, how often: "Paid media weekly — Acme", "Budget pacing — EMEA".

**Share carefully.** Viewers see data through the owner's connections, so scope `allowed_accounts` to exactly the accounts the dashboard shows, as the tool instructions require.
