---
name: supermetrics-event-and-webinar-roi
description: >-
  Measures field, event and webinar marketing from live data: pre-event cost per registration by
  channel, post-event regional lift, multi-event portfolio ROI, pipeline from attendees at 30,
  60 and 90 days, target-account engagement around an event, webinar and gated-content funnels
  by registration source, and partner or sponsored event ROI. Combines LinkedIn Ads, Google Ads
  and Meta Ads with HubSpot or Salesforce and the user's event-platform exports. Use when the
  user asks "which channel drives the cheapest webinar registrations", "did our conference lift
  engagement in that region", "how much pipeline came from the event", or "was the sponsorship
  worth it". Don't use for always-on lead gen and pipeline by channel (use
  supermetrics-b2b-pipeline-analysis).
---

# Event and webinar roi

## Workflows

### Pre-event channel ROI
**When to use:** the user is days or weeks from an event, wants to know where to put the remaining promotion budget, and needs to update the regional team on the plan.

**Platforms typically involved:** LinkedIn Ads and Google Ads most commonly. Facebook Ads occasionally.

### Step 1 — Pull the data
> Pull the last 30 days of performance data for campaigns containing [user's event naming convention — Event_, Webinar_, etc.] across [LinkedIn Ads, Google Ads, etc.].

Ask the user about the naming convention if unclear. Look for spend, clicks, and registrations (sometimes called conversions depending on platform setup).

If ad-platform registration numbers diverge significantly from the event platform (Zoom, Eventbrite, ON24 — not Supermetrics sources, so ask for the count or an export), flag the discrepancy before proceeding — the comparison is misleading otherwise.

### Step 2 — Compare cost per registration
Use `scripts/registration_cost_compare.py` (columns: `channel`, `spend`, `clicks`, `registrations`). The script warns when conclusions are based on fewer than 30 registrations per channel — the threshold below which CPR differences are likely noise.

### Step 3 — Visualize the comparison
The question the chart needs to answer: **which channel is generating registrations more efficiently, and how confident are we?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Pre-event channel ROI".

### Step 4 — Recommend budget shift
The recommendation should be concrete: dollar amount, source channel, destination channel, expected lift in registrations. If sample size is low, recommend a smaller hedged shift (e.g. 15% rather than 50%) and explain why.

### Step 5 — Update the regional team
Use `assets/regional_team_update.md`. The message is short — Slack-length even if delivered as email.

Post it to the regional team's chat channel if a messaging connector is available; otherwise give inline markdown to copy. To act on the budget shift, use the `supermetrics-campaign-management` skill with the user's approval.

---

### Post-event geo-targeted follow-up
**When to use:** the user just ran an in-person event in a specific region and wants to quantify whether it drove digital engagement lift locally.

**Platforms typically involved:** Facebook Ads, LinkedIn Ads at minimum. Google Ads if search campaigns ran. Campaigns need geo-targeting configured.

### Step 1 — Pull the regional data
> Pull the performance data from the last 14 days for the [Facebook Ads, LinkedIn Ads] campaigns geographically targeted to [region].

Be specific about the region's definition — city, metro, state. Pull the regional split with each platform's geo dimension (Facebook Ads `Region`, Google Ads location reports, LinkedIn Ads `memberRegion`). LinkedIn's member region is where engaged members are, not the targeting, and is hidden below privacy thresholds. To read a campaign's targeting settings, use `campaign_and_resource_get` (read-only).

### Step 2 — Compare to national baseline
Use `scripts/geo_lift_analyzer.py --target-region "<region>"` on a CSV with one row per region (`region`, `clicks`, `impressions`, `conversions`, `spend`) covering all regions, not just the target. The script normalizes regional metrics against a national baseline that excludes the target region (so the comparison isn't diluted by including the target in the baseline).

### Step 3 — Visualize the lift
The question the chart needs to answer: **did the event drive measurable digital lift in the region, and how big is the effect?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Post-event geo-targeted follow-up".

### Step 4 — Verdict
The script returns one of three verdicts: measurable lift / no detectable lift / insufficient data. Use this verdict explicitly in the deliverable — don't soften it. Field marketing measurement is more credible when the analyst names "we can't tell" rather than overclaiming.

### Step 5 — Build the post-event summary
Use `assets/post_event_lift_summary.md`. Include the verdict, the data, the confounders and what to measure differently next time.

Deliver inline markdown, or a Word document for a leadership readout.

---

### Multi-event portfolio analysis
**When to use:** the user has run 10-30 events over a quarter (or year) and wants to identify which event types and formats produce the best ROI. Used for next-period event planning and budget allocation.

**Platforms typically involved:** ad platforms (for promotion costs) + event platform / form platform for registration data + CRM for downstream pipeline (if available).

### Step 1 — Pull the event roster
> Pull the list of events held in [period]: event name, type (in-person / webinar / virtual conference / dinner / workshop), location, date, total spend (promotion + venue + production).

This roster usually lives in a spreadsheet or project tool, so ask the user for it (if it's in Google Sheets, Supermetrics can read it with the Google Sheets connector). Promotion spend per event can come from the ad platforms by campaign name.

### Step 2 — Pull registrations and attendance
> For each event, get registrations and attendance counts (from the event platform export or the CRM's campaign records). From the CRM (if connected), pull MQLs created within 30 days of the event from attendees.

### Step 3 — Compute composite ROI
Use `scripts/event_portfolio.py` (columns: `event_name`, `event_type`, `date`, `total_cost`, `registrations`, `attendees`, optional `location`, `mqls_within_30d`, `pipeline_value`). Its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Multi-event portfolio analysis".

### Step 4 — Visualize the portfolio
The question the chart needs to answer: **which event types and locations consistently produce the best outcomes?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Multi-event portfolio analysis".

### Step 5 — Build the portfolio review
Use `assets/event_portfolio_review.md` (offer a live Studio dashboard via the `manage_dashboards` for the promotion-spend side if this is reviewed every quarter). Its required sections are listed in [references/workflow-details.md](references/workflow-details.md), section "Multi-event portfolio analysis".

---

### Event-driven pipeline tracking
**When to use:** the user has run an event and wants to track the pipeline created by attendees over 30, 60, and 90 day windows. The deliverable usually goes to sales leadership to justify event investment.

**Platforms typically involved:** event platform for attendee list + CRM (HubSpot, Salesforce, Pipedrive) for pipeline tracking.

### Step 1 — Get the attendee list
> Get the attendee list for [event] (the user's event-platform export) including email addresses, company names, and job titles.

### Step 2 — Match attendees to CRM contacts
> For each attendee, find their CRM contact record. Pull: any opportunities created within 30/60/90 days of the event date, opportunity stage, opportunity amount.

Match by email, falling back to fuzzy company-name matching for personal emails. Supermetrics returns CRM report data (e.g. HubSpot `HSCON` contacts with email, Salesforce Opportunities and Campaigns reports), so pull the contact and opportunity tables and match them to the attendee list yourself. If the event is a Salesforce campaign, its responses, opportunities and won opportunities are available directly on the Campaigns report.

### Step 3 — Track pipeline progression
Use `scripts/event_pipeline_tracker.py --event-date YYYY-MM-DD --event-cost N` (columns: `attendee_email`, `matched_in_crm`, `opp_created_date`, `opp_amount`, `opp_stage`, optional `attendee_company`). Its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Event-driven pipeline tracking".

### Step 4 — Visualize the conversion timing
The question the chart needs to answer: **how does pipeline from this event accumulate over time?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Event-driven pipeline tracking".

### Step 5 — Build the report
Use `assets/event_pipeline_report.md`. Audience: VP of Sales or CRO + the marketing leader who approved the event budget.

---

### Account engagement scoring
**When to use:** the user runs B2B events where the goal is to deepen relationships with target accounts (vs raw lead capture). Specifically for ABM-aligned events.

**Platforms typically involved:** event platform for attendee data + CRM for target account list + LinkedIn Ads (if ABM ads were running) + GA4 for site behavior (if company-level identification is available).

### Step 1 — Define the target account list
Ask the user: which accounts are the priority for this event? Usually a tier-1 ABM list of 50-200 accounts.

### Step 2 — Compile engagement signals per account
For each target account, gather signals from the period spanning the event (e.g. 2 weeks before through 2 weeks after):
- Number of attendees from the account
- Seniority of attendees (CEO/VP/Director/IC)
- Pre-event LinkedIn ad impressions and clicks by `memberCompanyName` (where LinkedIn's privacy threshold allows)
- Post-event website visits (needs a company-identification tool; GA4 doesn't identify companies)
- Sales activities logged (meetings booked, emails exchanged)

### Step 3 — Score engagement per account
Use `scripts/account_engagement.py` (columns: `account_name`, `attendees_from_account`, `max_seniority`, `pre_event_ad_impressions`, `pre_event_ad_clicks`, `post_event_web_sessions`, `meetings_booked`, `in_pipeline`, optional `tier`, `opportunity_amount`). Its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Account engagement scoring".

### Step 4 — Visualize the program-level outcome
The question the chart needs to answer: **how many target accounts did the event meaningfully engage, and which are ready for sales follow-up?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Account engagement scoring".

### Step 5 — Build the brief
Use `assets/account_engagement_brief.md`. Audience: B2B marketing + sales counterparts.

---

### Webinar funnel deep dive
**When to use:** the user runs webinars regularly and wants a detailed performance review — not just registration cost but the full funnel through attendance, engagement, and downstream behavior.

**Platforms typically involved:** webinar platform (Zoom, ON24, Webex, GoToWebinar — not Supermetrics sources, so the user provides the attendance data) + form platform (HubSpot Marketing Forms or similar) + CRM for downstream tracking + ad platforms for promotion attribution.

### Step 1 — Pull the full funnel
> For [webinar], gather: total registrations by source, attendees (live and on-demand), average watch time, poll/Q&A participation rate, post-webinar email engagement, and MQLs created within 30 days of attendance.

### Step 2 — Cohort by registration source
Use `scripts/webinar_funnel.py` (columns: `source`, `spend`, `registrations`, `attended_live`, `attended_on_demand`, `high_engagement`, `mqls_within_30d`). The script breaks out funnel performance by registration source (LinkedIn Ads, Google Ads, Email, Organic, Partner referral, etc.); its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Webinar funnel deep dive".

### Step 3 — Visualize where each source drops off
The question the chart needs to answer: **which registration sources produce the highest-quality webinar audience, not just the cheapest registrations?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Webinar funnel deep dive".

### Step 4 — Build the summary
Use `assets/webinar_funnel_summary.md`. For a recurring webinar program, offer to publish the promotion and registration side as a live Studio dashboard (with `manage_dashboards`). The most-useful content is the source-level cost-per-MQL comparison and the engagement-rate breakdown.

---

### Partner / co-marketing event ROI
**When to use:** the user runs events with sponsorship partners or co-marketing partners (e.g. a joint webinar with a complementary vendor, a sponsored conference appearance). The ROI math is different because costs and audience reach are shared.

**Platforms typically involved:** ad platforms + event platform / form platform + CRM for pipeline. Partner-shared metrics may need to come from the partner directly.

### Step 1 — Define the shared and unshared costs
Ask the user:
- Sponsorship fee or co-marketing commitment ($)
- The user's own promotion spend on top of the sponsorship
- Estimated value of the partner's promotion reach (use only if partner shared the number)
- The user's production/staff costs

### Step 2 — Pull attribution data
> Pull registrations and form fills tagged with the [partner event] campaign source. From the CRM, pull MQLs and opportunities created from these contacts.

### Step 3 — Compute partner-specific ROI
Use `scripts/partner_event_roi.py costs.csv` (rows of `cost_category` = sponsorship_fee / user_promotion / user_production / partner_inkind, and `amount`) with `--user-registrations`, `--user-mqls`, `--user-pipeline`. Its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Partner / co-marketing event ROI".

Net-new contacts (not previously in CRM) and 60-day pipeline have to be counted from CRM data first; the script takes the totals.

If the user provided partner-side numbers (`--partner-registrations`, `--partner-mqls`, `--partner-pipeline`), the script also prints a "blended" view next to the "user-only" one.

### Step 4 — Frame the partner-specific lens
Show measurable outcomes (registrations, MQLs, pipeline) and strategic outcomes (reach, relationship, content reuse) side by side; a registrations-only view undersells partner events. Details: [references/workflow-details.md](references/workflow-details.md), section "Partner / co-marketing event ROI".

### Step 5 — Build the recap
Use `assets/partner_event_recap.md`. Often shared with the partner as a joint debrief; tone should be collaborative.

---

### Webinar / gated content funnel analysis
**When to use:** the user runs webinars, ebooks, whitepapers, or other gated content as part of demand gen, and wants to measure the full funnel from paid promotion through registration, attendance/download, and downstream MQL behavior.

**Platforms typically involved:** ad platforms + HubSpot Marketing Forms (views and submissions per form) or GA4 form key events + CRM for downstream tracking (HubSpot `HSCON` contact `first_conversion`/`recent_conversion` and lifecycle dates, or the Salesforce Campaigns report: responses, leads and opportunities per campaign) + email platform. Webinar attendance (Zoom, ON24, etc.) is not a Supermetrics source; ask the user for an export.

### Step 1 — Pull paid promotion data
> Pull the last 30-60 days of paid ad performance for campaigns promoting the specific content asset (webinar, ebook, etc.). Include spend, clicks, and platform-reported conversions.

### Step 2 — Pull registrations
> Pull form submissions for [content asset] from [form platform / CRM], including source for each registration (e.g. HubSpot contact `original_traffic_source`).

### Step 3 — Pull downstream behavior
For each registration, pull from the CRM:
- Did they attend / download?
- Did they become an MQL within 30 days?
- Did they enter pipeline within 60 days?

### Step 4 — Compute the funnel per channel
Use `scripts/gated_content_funnel.py` (columns: `channel`, `spend`, `clicks`, `registrations`, `attended_or_downloaded`, `mqls_within_30_days`, optional `opportunities_within_60_days`). Its outputs are listed in [references/workflow-details.md](references/workflow-details.md), section "Webinar / gated content funnel analysis".

### Step 5 — Visualize the funnel
The question the chart needs to answer: **which paid channels generate the highest-quality registrations for this content?**

Chart options and common variations: [references/workflow-details.md](references/workflow-details.md), section "Webinar / gated content funnel analysis".

### Step 6 — Output
Use `assets/integrated_funnel_summary.md` (the same template as the B2B email + ads funnel). For high-stakes content (annual flagship report, major webinar), offer a recap deck.

---

## Ad hoc analysis

For one-off questions outside these eight workflows (for example "what's the no-show rate by referring source?" or "which job titles registered through which channel?"), write fresh analysis code and pick the chart with the "Choosing the right visualization" section of [references/field-marketer-guide.md](references/field-marketer-guide.md).

---

## Audience and tone

Keep field marketing updates short and sideways-facing; for sales audiences, frame results as cost per MQL and pipeline. Details: [references/workflow-details.md](references/workflow-details.md), section "Audience and tone".

## Background

Chart choices, principles and gotchas for this area: [references/field-marketer-guide.md](references/field-marketer-guide.md).

## Before you analyze

- Load saved targets, budgets and known context with `get_business_context` and judge results against them, not generic benchmarks.
- Query correctly: follow the `supermetrics-marketing-data-queries` skill for discovery, field IDs and platform quirks.
- Check tracking first. If platform conversions and GA4 or backend numbers disagree by more than 20%, or spend continues with zero conversions, say so before any optimization advice.
- Pull, then analyze, as separate steps. Run one heavy query at a time.
- Evidence discipline: treat changes on fewer than ~10 conversions as noise, ignore items under 1,000 impressions, don't call learning-phase campaigns failures, check for shared budgets before budget advice, and re-pull before conceding a disputed number.
- Answer shape: conclusion, business impact, next actions, then "How I got this" (one line per data pull).
- Helper scripts in `scripts/` take a CSV and print a table. Run them if you can execute code; otherwise compute the same thing directly. Templates in `assets/` shape the final deliverable.

## After the analysis

- Analysis only recommends. If the user wants to act (pause, budget shift, negatives), use the `supermetrics-campaign-management` skill, with explicit approval for each change.
- For a recurring or shareable result, offer once to publish it as a live Supermetrics Studio dashboard with `manage_dashboards` (static preview first, then the live upload).
