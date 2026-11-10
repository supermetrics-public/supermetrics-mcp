# Event and webinar ROI — workflow details

Per-workflow detail for SKILL.md: script outputs, deliverable sections, chart choices and common variations, plus audience and tone notes. Read the section for the workflow you are running when SKILL.md points here (at its compute, visualization or deliverable step, or when the user asks for a variant).

## Pre-event channel ROI

### Chart choices

A sorted horizontal bar chart of cost-per-registration works as the primary visualization, with bars sorted ascending (cheapest first). When sample sizes are small for one or more channels, distinguish those bars visually — lower opacity, a hatched pattern, or a "low confidence" badge next to the label — so the user doesn't over-trust low-N comparisons.

For richer context, a paired bar chart per channel showing CPR + registration rate side-by-side surfaces the trade-offs (sometimes the cheaper channel also has lower-quality engagement).

If the user is tracking pacing toward a registration target, a line chart of cumulative registrations vs target trajectory makes the gap visible.

### Common variations

- **"What if we extend the campaign?"** — model expected additional registrations at current CPR. The script accepts a `--projected-days` flag.
- **Paid vs organic separately** — if the user has tagging that separates them, run the comparison separately rather than averaging.
- **Add cost per attendee** — needs post-event attendance data, which doesn't exist yet pre-event. If the user has historical attendance rates by channel, model expected attendees but state the assumption.

## Post-event geo-targeted follow-up

### Post-event summary contents

The verdict, the data, what is confounded (paid promotion that ran in the region, simultaneous events), and what to do differently next time for cleaner measurement.

### Chart choices

A paired bar chart per metric (CTR, conversion rate, CPC) with two bars per group — target region vs rest-of-country — makes the lift immediately visible. Color the target region in indigo and the baseline in a lighter shade so they read as two views of the same data.

When the user wants to see the magnitude of lift as a single number, a horizontal bar chart of percent-lift per metric (CTR +44%, conversion rate +24%, CPC -41%) is faster to read. Color by direction of lift relative to what's good (CTR up is green, CPC down is green).

### Common variations

- **Compare to same region last quarter, not to national** — different baseline, same workflow shape. Often a stronger test of "did the event do something."
- **Include earned media metrics** — usually outside Supermetrics. The user may have a PR or social listening tool with this data.
- **Project the pipeline impact** — risky. Pipeline attribution to a single event is fragile. If the user wants this, frame as a model with stated assumptions, not a measurement.

## Multi-event portfolio analysis

### Portfolio review sections

- **Best performers** (continue running)
- **Underperformers** (need improvement or sunset)
- **Experiments worth repeating** (one-off events with promising signals)
- **Recommended next-period mix** (specific event types and counts)

### Script outputs

- Cost per registration
- Cost per attendee (CPA)
- Attendance rate
- MQL conversion rate from attendees (if CRM data available)
- Cost per MQL via the event
- Composite score (cost per attendee and MQL rate, normalized within event type)

### Chart choices

A scatter plot works: x-axis = cost per attendee, y-axis = MQL conversion rate (or pipeline value if available). Dots colored by event type, sized by total attendees. The user sees the best-ROI quadrant immediately.

A secondary view: bar chart of average composite ROI by event type, sorted descending. Surfaces which formats deserve more investment.

### Common variations

- **By region** — break out the portfolio by region to see whether the best-performing event types differ geographically.
- **By persona** — if attendee data includes job titles, cluster by target persona and see which event types attract which personas.
- **Sponsorship vs hosted** — separately analyze events the user hosted vs sponsored. The economics are different.

## Event-driven pipeline tracking

### Script outputs

- Total attendees
- Attendees matched to CRM contacts
- New opportunities created (in each 30/60/90 day window)
- Total pipeline value per window
- Closed-won deals (if any have closed)
- Pipeline ROAS (pipeline value / event cost)

### Chart choices

A cumulative line chart works well: x-axis = days since event, y-axis = cumulative pipeline value. Mark the 30/60/90 day milestones. Shows whether the event produced fast-moving pipeline or slow-developing pipeline.

A secondary view: funnel chart from attendees → matched → opp created → closed-won, with the conversion rate at each step.

### Common variations

- **Compare events** — same workflow, multiple events. Surface which events drove the most pipeline relative to investment.
- **Include net-new contacts** — for accounts not previously in CRM that came in via the event. These are pure event acquisition; existing accounts that attended are influence vs acquisition.
- **Per-persona pipeline** — if event attendees include multiple personas (e.g. CMO, VP Marketing, Director), break out pipeline by attending persona to see which roles convert.

## Account engagement scoring

### Script outputs

- Engagement score (0-100, composite of all signals)
- Tier classification (engaged / warming / aware / cold)
- Whether the account is in active pipeline
- Last meaningful touchpoint

### Chart choices

A funnel chart works: target accounts → reached pre-event → attended → engaged post-event → in active pipeline. Each tier showing conversion rate.

A secondary view: the top 20 most-engaged accounts not yet in pipeline. This is the hand-off list for sales.

### Common variations

- **Tier-specific KPIs** — Tier 1 accounts may need a different success bar than Tier 2 (e.g. attendance + meeting booked for Tier 1; attendance alone for Tier 2).
- **Compare to non-target attendees** — separate engagement metrics for target-account attendees vs general attendees. Often the program optimizes for general count when the actual goal is target account depth.
- **Per-persona engagement** — track whether multiple personas from the same account attended (the C-suite + VP combination is often a stronger signal than two ICs).

## Webinar funnel deep dive

### Script outputs

- Registration count
- Attendance rate (live)
- On-demand attendance rate (post-event watch)
- Engagement rate (Q&A participation, poll responses)
- MQL conversion rate
- Cost per MQL via webinar (where promotion cost is allocable)

### Chart choices

A horizontal grouped bar chart per source, with bars for each funnel stage (register, attend live, attend total, engage, MQL), colored consistently. Surfaces patterns like "LinkedIn Ads registers fewer but attends at 2x the rate" or "Email registers most but engages least."

### Common variations

- **Live vs on-demand split** — many webinars deliver more value on-demand than live. Surface the on-demand performance separately; it's often the dominant funnel.
- **Speaker analysis** — if the user runs multiple webinars with different speakers, compare attendance and engagement by speaker. Some speakers consistently drive better outcomes.
- **Topic clustering** — if the user has run 5+ webinars in a quarter, cluster by topic to see which themes drive engagement.

## Partner / co-marketing event ROI

### Framing the partner lens

A pure "registrations" view undersells partner events because much of the value is being in front of the partner's audience for brand awareness — hard to measure but real. The visualization should show both:
- The measurable outcomes (registrations, MQLs, pipeline) as bar charts
- The strategic outcomes (audience reach, partner relationship, content reuse) as qualitative notes

The chart on its own won't answer "was the partnership worth it" — the doc has to frame it.

### Script outputs

- Total cost (the user's investment; partner in-kind is shown separately)
- Cost per registration and cost per MQL
- Pipeline ROAS

### Common variations

- **Sponsored conference** — different shape than joint webinars. Booth traffic, badge scans, on-site meetings booked all matter.
- **Renewal decision** — if the user is deciding whether to renew a sponsorship, the recap doc is the input. Add an explicit renew/decline section with the decision criteria.
- **New partner test** — if this was a first-time partnership, surface what worked operationally (lead handoff, content creation, audience access) in addition to numbers. Often the operational fit matters more than the first-event numbers.

## Webinar / gated content funnel analysis

### Script outputs

- Spend
- Clicks
- Registrations (and registration rate from clicks)
- Attendees/downloaders (and rate from registrations)
- MQLs within 30 days (and rate from registrations)
- Cost per MQL via this content

### Chart choices

A grouped horizontal bar chart per channel, with bars for each funnel stage (register, attend, MQL within 30 days) sized by conversion rate, surfaces where the drop-off happens per channel. Sometimes a channel has high registration rates but low attendance — the content didn't deliver against expectations. Other times the inverse.

A secondary view: cost-per-MQL bar chart per channel via this content asset, compared to the user's overall cost-per-MQL baseline. Surfaces whether this content is more or less efficient than the user's blended acquisition.

### Common variations

- **Multi-asset analysis** — instead of one content asset, compare 5-10 across the same time period. Which content assets are the highest-converting?
- **By topic or persona** — group content by topic or target persona; see which themes resonate at the highest conversion rates.
- **Live vs on-demand webinar** — many webinars have a live event then an on-demand version. The on-demand conversion rate tends to be different (and is often where the volume actually comes from). Surface both.

## Audience and tone

### Field marketing audience

Field marketers communicate sideways — to regional sales reps, local partners, event ops — more than upward to leadership. The tone is faster and less formal than what marketing leadership produces. Match that tone: short, specific, no preamble. A "regional team update" is closer to a Slack message than a memo, even when delivered as email.

### Demand gen audience

Demand gen sits between marketing and sales — two audiences with different vocabularies. Marketing leaders want efficiency framed as CPA and ROAS; sales leaders want it framed as cost per MQL, cost per opp, pipeline contribution. When the deliverable is for sales, translate up the funnel. When it's for marketing, platform-level metrics are fine.
