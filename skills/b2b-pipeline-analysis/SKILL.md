---
name: b2b-pipeline-analysis
description: B2B demand gen analysis from live ad, GA4 and CRM data (HubSpot, Salesforce, Pipedrive): lead-gen efficiency, pipeline and revenue by channel, MQL-to-SQL by source, ABM engagement, and email plus paid funnels. Use for pipeline contribution, lead quality and ABM questions.
---

# B2b pipeline analysis

## Workflows

### Lead generation efficiency comparison
**When to use:** the user is comparing efficiency of B2B advertising platforms for lead capture, typically LinkedIn vs Facebook. Output is usually a one-page summary for the demand gen team or a leadership recommendation.

**Platforms typically involved:** LinkedIn Ads and Facebook Ads primarily. Sometimes Google Ads for B2B search.

### Step 1 — Pull the data with campaign filter
> Pull the last 60 days of campaign performance from LinkedIn Ads and Facebook Ads, filtered to campaigns containing the phrase [Lead Gen / LG_ / MQL_ / whatever the user's convention is].

The filter phrase is specific to the user's naming convention. Ask if unclear. The data needs to be at the ad / asset level, not just campaign level — different assets in the same campaign often perform very differently.

### Step 2 — Compare CPL and lead conversion rate
Use `scripts/lead_efficiency.py` (columns: `platform`, `spend`, `clicks`, `impressions`, `leads`). Outputs platform-level CPL, lead-to-click ratio, CTR, and a comparison summary. For LinkedIn Lead Gen Forms, the lead count is the `oneClickLeads` field.

### Step 3 — Visualize the comparison
The question the chart needs to answer: **which platform is more efficient, and by how much?**

A simple sorted bar chart of CPL by platform works as the primary visualization. If the user wants the multi-dimensional view, a small dashboard-style group (CPL, lead conversion rate, CTR, total leads) with the platforms side-by-side makes the trade-offs visible — sometimes the cheaper platform produces fewer leads, or the lower-CPL platform has worse lead quality. Name the trade-off explicitly when surfacing the visualization.

If the user has tagged the ad creatives, a secondary chart ranking individual ads by efficiency (across both platforms) surfaces the actual winners, which is often more useful than the platform-level average.

### Step 4 — Identify what's working in the winners
> Identify the top two best-performing ad creatives across both platforms based on conversion rate, and explain why their messaging is likely resonating with the target audience.

This is the qualitative step. Look at the winning ad copy and identify the actual angle — pain point, social proof, status, ROI claim. The output is a brief on what's resonating, which the user can take into the next round of creative.

Use `assets/lead_efficiency_summary.md` to structure the final deliverable.

### Step 5 — Output
Default delivery is markdown inline. If the user wants a Word doc, use the `docx` skill.

### Common variations
- **Add Google Ads search to the comparison** — search and social lead gen aren't directly comparable on raw CPL. Frame as "intent-driven (search) vs interruption-driven (social)" rather than head-to-head.
- **Cost per MQL, not cost per lead** — needs CRM data. If a HubSpot or Salesforce connector is active, pull MQL counts by source. Without one, ask the user how they'll get the data.
- **Compare to last quarter** — re-run for the prior 60-day window and overlay both periods on the chart.

---

### Pipeline contribution by channel
**When to use:** the user is in B2B and wants to credit marketing channels with the pipeline they actually create — not just leads. This is the single most-requested demand gen analysis when a CRM connector is available.

**Platforms typically involved:** ad platforms (LinkedIn Ads, Google Ads, Facebook Ads) + a CRM connected in Supermetrics (HubSpot, Salesforce, Pipedrive, Zoho CRM, Close CRM, Odoo CRM). Useful fields: HubSpot `deal_amount`, `deal_stage`, `original_source`; Salesforce `opportunity_amount`, `opportunity_stage_name`, `opportunity_lead_source`. Confirm exact names with `field_discovery`.

### Step 1 — Pull spend and pipeline
> Pull the last 90 days of ad spend by channel from [ad platforms]. Then pull opportunities created in the same period from [CRM], including opportunity amount, stage, and source/first-touch channel.

Confirm with the user how their CRM attributes channels to opportunities — most common patterns:
- **First-touch:** the channel that brought the contact in
- **Last-touch:** the channel of the activity immediately before opportunity creation
- **Multi-touch:** weighted across all touchpoints

State the attribution method explicitly in the output.

### Step 2 — Compute cost per opportunity and pipeline ROAS
Use `scripts/pipeline_attribution.py --spend spend.csv --opps opportunities.csv` (optional `--stage-filter`). Outputs per channel:
- Total spend
- Number of opportunities attributed
- Total pipeline value (sum of opportunity amounts)
- Cost per opportunity (CPO)
- Pipeline ROAS (pipeline value / spend)

### Step 3 — Visualize the credit
The question the chart needs to answer: **which channels create the pipeline, and at what cost?**

A useful primary visualization is a paired bar chart per channel: spend (one bar) and pipeline created (second bar, often on a secondary axis since the scales differ). Channels where the pipeline bar is much taller than spend are the leverage channels.

A secondary view: stacked bar of pipeline value by channel, sorted by total. Surfaces which channels concentrate vs spread their pipeline contribution.

### Step 4 — Frame caveats
This view has limitations that need to be surfaced:
- It's not closed-won revenue, it's pipeline. Some pipeline will close; some won't.
- The attribution method shapes the rankings. First-touch credits top-of-funnel channels; last-touch credits bottom-of-funnel channels.
- Opportunities can sit in pipeline for months. The 90-day window captures opps created, not opps closed.

### Step 5 — Build the report
Use `assets/pipeline_attribution_report.md`. The audience is typically the VP of Sales or CRO; tone should be data-forward with explicit acknowledgment of attribution choices. For a recurring pipeline review, offer a live Studio dashboard (with `manage_dashboards`).

### Common variations
- **Closed-won, not pipeline** — re-run with `--stage-filter` set to the CRM's closed-won stage name. Smaller numbers, longer time lag, but more defensible. Best for quarterly reviews.
- **Win rate by channel** — additional column: of opps from this channel, what % closed? Some channels generate lots of low-quality pipeline; this surfaces it.
- **Deal size by channel** — average opportunity amount per channel. Often the highest-CPO channels generate the largest deals.

---

### MQL-to-SQL conversion rate by source
**When to use:** the user needs lead quality measurement, not just lead volume. CPL is the cheap version of this question; MQL-to-SQL conversion rate is the real answer.

**Platforms typically involved:** ad platforms + CRM (HubSpot, Salesforce). Marketing automation (Marketo via the Adobe Engage connector, Eloqua, HubSpot Marketing Email) if lead nurture is part of the journey.

### Step 1 — Pull leads, MQLs, and SQLs by source
> Pull the last 90 days of leads created from [CRM], grouped by lead source. For each lead, include whether they reached MQL status and SQL status, and the dates of each transition.

In HubSpot (`HSCON`), the lifecycle date properties (`Became a lead / marketing qualified lead / sales qualified lead date`) and `original_traffic_source` give this per contact; in Salesforce, use the Leads report with `lead_source`, `lead_status` and `lead_converted_date`. HubSpot also has aggregate counts and average days to MQL/SQL in `HS`.

The 90-day window matters — leads need time to progress through the funnel. Pulling last week's data won't show conversion rates because leads haven't had time to convert.

### Step 2 — Compute conversion rates per source
Use `scripts/mql_quality.py` (columns: `source`, `lead_created_date`, `mql_date`, `sql_date`, optional `spend` or `--spend-csv`). Outputs per source:
- Total leads
- MQL conversion rate (% of leads that became MQLs)
- SQL conversion rate (% of MQLs that became SQLs)
- Lead-to-SQL conversion rate (% of leads that became SQLs)
- Time-to-MQL (median days)

### Step 3 — Visualize quality vs volume
The question the chart needs to answer: **which sources produce high-quality leads, and which produce volume without quality?**

A scatter plot works well here: x-axis = lead volume, y-axis = MQL conversion rate. Bubble size = total spend on the source. Sources in the top-right (high volume + high quality) are the keepers; bottom-right (high volume + low quality) are the spend-too-much candidates.

A secondary view: funnel chart per source — leads → MQLs → SQLs. Visualizes where the drop-off happens per source.

### Step 4 — Diagnose
For sources with low MQL conversion, the question is whether the issue is:
- **Lead targeting** (the source brings in wrong-fit leads)
- **Lead nurture** (the leads aren't getting touched, or the nurture doesn't resonate)
- **MQL definition** (the bar is set too high)

The script doesn't diagnose this automatically — surface the question for the user to investigate.

### Step 5 — Output
Use `assets/lead_quality_diagnostic.md`. Audience: marketing operations or demand gen leadership.

### Common variations
- **Add deal-source connection** — for sources where SQLs convert, what's the average deal size? Quality has multiple dimensions.
- **Cohort by month** — recent-cohort conversion rates may differ from older cohorts. Plot trend.
- **Compare paid vs organic sources** — paid sources should have higher CPL but often have lower MQL rates because of intent quality.

---

### ABM campaign performance
**When to use:** the user runs account-based marketing (ABM) and needs to measure coverage and engagement across a defined target account list.

**Platforms typically involved:** LinkedIn Ads (the primary ABM ad platform) + CRM with target account list + GA4 or web analytics for engaged-session data.

### Step 1 — Get the target account list
Ask the user (multiple-choice if the client supports it): is the target account list maintained in the CRM (as an account property or list membership) or elsewhere? How many accounts are on it?

### Step 2 — Pull engagement signals
For each target account, gather available engagement signals:
- **From LinkedIn Ads:** impressions and clicks by `memberCompanyName` (company demographics; LinkedIn hides values below its privacy threshold, so small accounts may not appear)
- **From the CRM:** activities logged against the target account, meetings booked, opportunities created
- **Website visits by company:** GA4 doesn't identify companies. Use a reverse-IP tool's export (e.g. Clearbit, 6sense) or HubSpot's `HSCON` contact/company data if available

### Step 3 — Compute account-level coverage and engagement
Use `scripts/abm_engagement.py`. Outputs per account:
- Coverage (have we reached them at all)
- Engagement score (composite from impressions, clicks, site visits, activities)
- Pipeline status (opportunity / no opportunity)
- Last meaningful touchpoint

Aggregated:
- % of target accounts reached
- % of target accounts engaged
- % of target accounts in active pipeline

### Step 4 — Visualize the funnel
The question the chart needs to answer: **of our target accounts, how many are at each stage of engagement?**

A funnel chart works: target list size → reached → engaged → opportunities → closed-won. Each tier showing the absolute count and the conversion rate.

A secondary view: a list view (top 20 most-engaged target accounts that aren't yet in pipeline), since those are the highest-value follow-up targets for sales.

### Step 5 — Build the report
Use `assets/abm_engagement_report.md`. Audience: B2B marketing leader + sales counterparts running the ABM program together. ABM programs are reviewed every few weeks, so offer to publish the ad and CRM side as a live Studio dashboard (with `manage_dashboards`).

### Common variations
- **Tier the target accounts** — Tier 1 (top 50) vs Tier 2 (next 200) may need different KPIs. Higher tiers should show deeper engagement.
- **Compare ABM-touched vs untouched** — for accounts on the target list, do those that received ABM touches close at higher rates than those that didn't?
- **Persona reach within accounts** — coverage isn't just account-level; it's persona-level within accounts. The CMO and the VP of Engineering need different messaging. Pull LinkedIn engagement by `memberSeniority` or `memberJobFunction` (aggregate, not per account).

---

### Email + ads integrated funnel
**When to use:** the user runs both paid acquisition and email nurture and wants to see how they interact — does email nurture make paid clicks convert better? Does paid acquisition feed the email list usefully?

**Platforms typically involved:** ad platforms + email marketing platforms (HubSpot Marketing Email, Adobe Engage/Marketo, Mailchimp, Klaviyo, Brevo, Campaign Monitor, ActiveCampaign, Omnisend) + CRM for the conversion data.

### Step 1 — Pull the email side
> Pull the last 90 days of email campaign performance from [email platform]: campaigns sent, opens, clicks, conversions, and revenue attributed.

Also pull list growth (e.g. Mailchimp's ListGrowthHistory report). Subscriber source by channel usually comes from the CRM (HubSpot `original_traffic_source`) or GA4 sign-up events by source, not from the email platform.

### Step 2 — Pull the paid side
> Pull the last 90 days of paid acquisition data, focusing on conversions that include both "purchase" / "lead" type and "newsletter signup" / "email subscription" type.

### Step 3 — Join the views
Use `scripts/email_paid_funnel.py` (columns: `channel`, `clicks`, `spend`, `subscribed`, `converted_direct`, `converted_via_email`, `still_nurturing`). The per-channel subscriber and conversion counts usually have to be assembled from CRM or GA4 data first. The script:
- Computes paid-to-email-subscriber rate (of paid clicks, how many turned into email subscribers)
- Computes email-to-conversion rate (of nurtured subscribers, how many converted within the period)
- Identifies the channels that subscribe-then-convert at high rates (efficient acquisition through nurture) vs convert directly (efficient acquisition without nurture)

### Step 4 — Visualize the interaction
The question the chart needs to answer: **does our email nurture amplify paid acquisition, or is it a separate channel?**

A Sankey-style flow diagram is ideal here — paid channels on the left, "Subscribed", "Direct converted", or "Lost" in the middle, "Converted via email", "Converted directly", "Still nurturing" on the right. The flow widths show volume.

If a Sankey is too complex, a stacked bar per paid channel showing the split (% direct converted, % subscribed and later converted, % subscribed and still nurturing, % lost) works as a simpler alternative.

### Step 5 — Build the narrative
Use `assets/integrated_funnel_summary.md`. The interesting finding is usually one of:
- "Email amplifies acquisition — paid channels with the highest subscribe rate also have the highest eventual conversion rate via nurture"
- "Email and paid are separate channels — subscribers convert at similar rates regardless of paid source"
- "Paid acquisition is bypassing email — high direct-conversion rates mean email is mostly serving repeat business"

### Common variations
- **Per-segment email performance** — if the email platform supports segmentation, surface which segments (e.g. newsletter subscribers vs free-trial users) convert at higher rates.
- **First purchase vs repeat** — for ecommerce, separate first-purchase email influence from repeat-purchase email influence. Often very different patterns.
- **Welcome series performance specifically** — the welcome series is the highest-leverage email touchpoint. Pull its open and click rates separately.

---

## Background

Chart choices, principles and gotchas for this area: [references/demand-gen-guide.md](references/demand-gen-guide.md).

## Before you analyze

- Load saved targets, budgets and known context with `manage_business_context` (`action: get`) and judge results against them, not generic benchmarks.
- Query correctly: follow the `marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
