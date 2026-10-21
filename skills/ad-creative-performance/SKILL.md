---
name: ad-creative-performance
description: Analyze ad creative from live data: ad fatigue (rising frequency, falling CTR), creative concept tests, winning hooks and formats, and refresh recommendations for Meta, TikTok, LinkedIn and Google ads. Use when ads are tiring, a creative test needs a verdict, or the creative team needs a brief.
---

# Ad creative performance

## Workflows

### Ad fatigue and frequency check
**When to use:** the user suspects ad creatives are burning out — previously strong ads losing CTR, climbing frequency, rising CPC. Often ends with notifying the creative team about which ads need refresh.

**Platforms typically involved:** Facebook Ads (`Frequency`), TikTok Ads (`frequency`), Google Ads (`avg_impr_frequency_per_user`). LinkedIn Ads has no frequency field; compute impressions ÷ reach (`approximateUniqueImpressions`).

### Step 1 — Pull the data
> Pull the last 30 days of performance data grouped by ad name, with impressions, frequency, CTR, and CPC.

Include `ad_id` if the user will need to act on this in-platform.

Frequency can't be summed or averaged across days (daily values understate it). Pull it per ad for the recent window as a separate query and put that value in the `frequency` column of the recent-window rows before running the script.

### Step 2 — Identify fatigued ads
Use `scripts/ad_fatigue_detector.py`. Default thresholds: frequency >3.5 and CTR decline >20% week-over-week. If the user wants different thresholds, accept them and pass to the script.

### Step 3 — Visualize the fatigue landscape
The question the chart needs to answer: **which ads are fatigued, how severely, and how much spend is exposed to the problem?**

The most informative visualization for this is usually a scatter plot — frequency on the x-axis, recent CTR on the y-axis, dot size weighted by spend, dot color encoding a third dimension (CPC trend, or simply flagged vs not). Shade or label the fatigue zone explicitly. The user sees at a glance which ads are in trouble and which are still healthy.

Alternative when the user has few ads (under 10): a horizontal bar chart ranking ads by severity, with spend annotated next to each bar.

Whichever you pick, name the worst offender explicitly when surfacing the chart — don't make the user visually scan.

### Step 4 — Refresh recommendations
For each flagged ad, state what's happening (frequency X, CTR down Y%, CPC up Z%) and what to do (pause, refresh creative, refresh copy only, swap thumbnail). Prioritize by spend impact — the $5k/week ad needs attention before the $50/week one.

### Step 5 — Notify the creative team
Build the email body using `assets/creative_refresh_email.md`. The default delivery is markdown inline so the user can review and send themselves. If the user wants it as a doc, use the `docx` skill. If Gmail is connected and the user wants it as a draft, offer to save it there.

### Common variations
- **No frequency threshold known** — use 3.5 as a sensible default or compute +1 standard deviation above the account's typical range.
- **Just top 10 fatigued ads** — limit the script output and the chart to top N by spend or severity.

---

### Creative concept testing and iteration
**When to use:** the user is running a creative test (multiple variants of an ad in the same campaign) and wants to identify winners and generate new variations.

**Platforms typically involved:** Facebook Ads, LinkedIn Ads, sometimes TikTok Ads.

### Step 1 — Pull the data
> Pull the last 14 days for [campaign name] across [platforms], broken down by individual ad name and ad format.

Format matters here — is video beating static? The field differs by platform (Facebook Ads `creative_object_type`, Google Ads `AdFormatType`); name the column `ad_format` for the script. Facebook Ads also returns `creative_thumbnail_url`.

### Step 2 — Rank by efficiency
Use `scripts/cpa_ranker.py` with `--group-by ad_format` to get both: top ads by CPA and the format-level comparison.

### Step 3 — Visualize the results
Two questions to answer here, often best as two charts rather than one crowded one:
- **Which ads are winning?** A ranked horizontal bar chart sorted by CPA ascending, with format encoded as bar color.
- **Which format wins on average?** A small comparison chart with format on the x-axis and the efficiency metrics (CPA, CTR, conversion rate) on the y-axis.

The user often needs both views — "video has 18% better CPA on average, but the single best ad is a static" is a real and important finding that one chart alone can miss.

### Step 4 — Write new variations
Use `assets/ad_copy_variations.md`. Identify what's working in the winners (the hook, the angle, the audience tilt — not just the words) and preserve that. Vary CTA, framing, or proof point. Don't synonym-swap.

### Step 5 — Output the variations
Default delivery is inline markdown. If the user wants a spreadsheet they can hand to ops for upload, use the `xlsx` skill with columns for headline, body, CTA, target audience, and hypothesis.

### Common variations
- **Rank by ROAS instead of CPA** — pass `--metric roas` to the ranker.
- **New audiences vs lookalikes** — re-run with `audience` as the grouping dimension.
- **Headlines only, not body copy** — the variations template has both sections; pick the relevant one.
- **Industry benchmark CTR** — if the user wants context for whether their numbers are good, use web search to find recent industry benchmark data and overlay as a reference line on the chart.

---

## Background

Chart choices, principles and gotchas for this area: [references/performance-marketer-guide.md](references/performance-marketer-guide.md).

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
