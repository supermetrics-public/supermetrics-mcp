---
name: funnel-and-tracking-audit
description: Reconcile what ad platforms report with what GA4 and the CRM or store recorded, find tracking breaks, implausible CPAs and broken UTMs, and diagnose where traffic leaks between click and conversion. Use before trusting optimisation advice, or when platform and GA4 numbers disagree.
---

# Funnel and tracking audit

## Workflows

### The traffic-to-conversion audit
**When to use:** the user wants to see which channels drive engaged traffic that actually converts on-site, not just cheap clicks. The output usually ends with an update to sales leadership justifying a budget reallocation.

**Platforms typically involved:** Google Ads, LinkedIn Ads, and Google Analytics 4. GA4 is mandatory — without it this workflow can't run.

### Step 1 — Pull ad platform data, then GA4
> Pull the last 30 days of spend and click data from the Google Ads and LinkedIn Ads accounts. Then pull sessions and key events (conversions) for the same period from Google Analytics 4, by session source / medium.

Two separate pulls. Do ad platforms first, confirm the data, then GA4 split by `sessionSourceMedium` or `sessionCampaignName` (not default channel grouping, which lumps LinkedIn and Facebook into "Paid Social"). Map GA4 rows to the same channel names as the ad data before reconciling. If GA4 isn't connected, stop and tell the user this workflow requires it.

### Step 2 — Reconcile
Use `scripts/funnel_reconciler.py --ad-platform ads.csv --ga4 ga4.csv`. The script joins by channel name (the `channel` column in both files must use identical values) and computes the divergence between platform-reported and GA4-measured outcomes. Channels where the click-to-session gap exceeds 25% get flagged as likely UTM/tracking issues.

### Step 3 — Visualize the reconciliation
The question the chart needs to answer: **where's the leak — is each channel converting the traffic it bought, or losing it somewhere?**

For a small number of channels (3–5), a paired bar chart per channel works well: one bar for the ad platform metric (e.g. platform-reported CPA), one bar for the GA4 measurement of the same thing (GA4-measured CPA). Big gaps between the pairs are the story. Channels with tracking flags get an amber outline.

When the user wants the funnel view explicitly, a descending bar chart showing impressions → clicks → sessions → conversions makes drop-off rates visible at each stage.

Whichever you build, name the headline above the chart — "Two of four channels have tracking issues" or "Facebook traffic doesn't convert post-click" — don't make the user infer the story.

### Step 4 — Recommendation
> Write a short paragraph recommending how budget should be reallocated next period to prioritize the channel driving the highest-quality website traffic.

The recommendation should name a specific dollar amount, a source channel, a destination channel, and the expected impact on **GA4-measured** conversions (not just ad-platform conversions — that's the whole point).

### Step 5 — Update sales leadership
Build from `assets/sales_leadership_email.md`. Default delivery is markdown inline. If the user wants it as a Word doc, use the `docx` skill. If Gmail is connected, offer to save as a draft. If leadership wants to see this every month, offer to publish it as a live Studio dashboard (see the `manage_dashboards`).

### Common variations
- **Adobe Analytics instead of GA4** — same workflow shape; Supermetrics has Adobe Analytics support, ask whether it's connected.
- **Use last-click instead of GA4's default attribution** — GA4's default is data-driven; the model is a property-level setting, so the API returns whatever the property uses. Session-scoped dimensions (`sessionSourceMedium`) are last-click by nature; ask the user which view they want and note the choice in the deliverable.
- **Join with CRM lead-quality data** — if HubSpot or Salesforce is connected, pull MQL conversion rate by source and join with the reconciliation. The combined view ("$50 ad CPA + 8% MQL rate" vs "$80 ad CPA + 35% MQL rate") changes the recommendation entirely.

---

### Landing page performance diagnosis
**When to use:** the user wants to find which content pages get traffic but fail to convert it, and document the fixes for the web/dev team.

**Platforms typically involved:** Google Analytics 4 (mandatory).

### Step 1 — Pull page-level data
> Pull the top 20 landing pages by traffic volume from Google Analytics 4 over the last 30 days, including bounce rate, average engagement time, and key events (or session key event rate).

Default to top 20 — that's the right unit for "where is the leak?" because it weights toward pages where fixes will have visible impact. Expand if the user wants a deeper audit.

### Step 2 — Identify underperformers
Use `scripts/landing_page_diagnoser.py` (columns: `page_path`, `sessions`, `bounce_rate` and `conversion_rate` as percentages, `avg_time_on_page` in seconds; it accepts `conversions` instead of a rate). Default thresholds are 70% bounce rate and 1% conversion rate, with a 500-session minimum. Ask the user about their baselines — these vary by industry (a B2B SaaS landing page should convert higher than a blog post).

### Step 3 — Visualize the diagnosis
The question the chart needs to answer: **which pages should the web team fix first, and why?**

The most useful visualization for this is usually a quadrant chart: sessions on the x-axis, conversion rate on the y-axis. The bottom-right quadrant (high traffic, low conversion) is the priority. The top-right is winners worth studying. Pages get sized or colored by lost-opportunity score (sessions × conversion gap) so the user sees impact at a glance.

When the user wants a ranked list rather than a quadrant view, a sorted horizontal bar chart of lost-opportunity score works well — bars sorted descending, top 5 highlighted as priority.

Reference lines on either chart for the conversion target and bounce-rate threshold make the cut-offs visible without explanation.

### Step 4 — Diagnose likely causes
For each priority page, list 2–3 specific reasons it might be underperforming based on standard UX principles:
- Slow page load, especially on mobile
- Confusing or missing primary CTA
- Headline doesn't match search intent or ad copy driving the traffic
- Form too long or asking for too much info
- Above-the-fold content doesn't deliver on the page title's promise
- Mobile experience broken (form fields too small, buttons cut off)
- No social proof or trust indicators near the CTA
- Above-the-fold ads or popups that interrupt
- Search intent vs page intent mismatch

Pick fixes appropriate to the page type — a blog post needs different fixes than a product landing page.

### Step 5 — Build the web team doc
Use `assets/web_team_doc.md` for structure. The doc needs to be a checklist the web team can act on: page URL, current state, proposed fix, estimated effort, expected impact.

Default delivery: build it as a Word doc with the `docx` skill — the web team will print or share it. If the user prefers Drive, save it there if Google Drive is connected. If they want a spreadsheet to track checkboxes, use the `xlsx` skill instead.

### Common variations
- **CMS or HubSpot tracks conversions, not GA4** — same workflow shape; pull form views and submissions from HubSpot Marketing Forms (`HSMF`) or page data from HubSpot Content Analytics (`HSCA`) if connected, then join to the GA4 page list by URL.
- **Just blog posts, not landing pages** — filter URLs containing `/blog/` (or the user's blog path) in step 1.
- **Mobile only** — re-pull GA4 data with device category as a filter.

---

## Background

Chart choices, principles and gotchas for this area: [references/demand-gen-guide.md](references/demand-gen-guide.md), [references/content-marketer-guide.md](references/content-marketer-guide.md).

## Before you analyze

- Load saved targets, budgets and known context with `get_business_context` and judge results against them, not generic benchmarks.
- Query correctly: follow the `marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
