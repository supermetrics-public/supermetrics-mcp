# SEO and content performance — workflow details

Chart choices and common variations for each workflow in SKILL.md. Read the section for the workflow you are running when you reach its visualization step, or when the user asks for a variant of the workflow.

## Paid promotion content analysis

### Chart choices

A sorted horizontal bar chart of the composite promotion score works well as the primary visualization, with bars colored by asset type (whitepaper, ebook, guide, report) so format trends are visible.

If the user wants the multi-dimensional view, a grouped bar chart with one cluster per asset showing CPC, CTR, and lead rate side-by-side surfaces the trade-offs — an asset might win on CTR but lose on lead rate.

### Common variations

- **Organic vs paid** — different workflow; organic and paid content perform on different signals. Suggest running paid first, then a separate organic audit.
- **Include cost per lead, not just engagement** — if the user has lead-volume data per asset, the ranker accepts it. But sample sizes get small fast at the asset level for leads; CPC/CTR usually has more reliable signal.

## Organic content and SEO performance audit

### Chart choices

A scatter plot works well: x-axis = monthly impressions trend (% change), y-axis = monthly clicks trend. Dots sized by current monthly clicks, colored by classification. The user sees at a glance which pages are climbing and which are slipping.

A secondary view: top 20 decaying pages ranked by absolute click loss vs peak month. These are the urgent updates.

### Common variations

- **Add backlink context** — if Ahrefs is connected, pages with strong backlinks deserve preservation effort; pages with no backlinks may be sunset candidates.
- **Compare to competitors** — if Semrush or Ahrefs is connected, surface which competitor pages outrank the user's on key queries; gives a roadmap for what the user's pages need to address.
- **Branded vs non-branded** — separate branded query performance from non-branded. Branded performance is a vanity metric for content; non-branded is the real test.

## Content-to-conversion attribution

### Chart choices

A bar chart of "conversions influenced" (presence in conversion path) per content piece, with bars shaded by share of those that were first-touch vs middle-touch vs last-touch. Often surprising — long blog posts appear in many paths as middle-touch even when they're never the last touch.

### Common variations

- **Time-to-conversion by content type** — does engaging with this content type accelerate the conversion timeline? Pull average days from first content engagement to conversion per content type.
- **Per-persona content paths** — if persona data is available, the same content may have very different attribution patterns per persona.
- **Repurposing signals** — if a top-converting content piece is in a single format (e.g. long blog post), surface "convert this to a webinar / podcast / video" as a recommendation.

## Newsletter and email content performance

### Chart choices

A bar chart of open rate by content type works as a starting point. A second view: a scatter plot with subject line length on the x-axis and open rate on the y-axis, sized by send volume, colored by content type. Surfaces whether length correlates with open in this audience.

For unsubscribe analysis: a horizontal bar chart of campaigns ranked by unsubscribe rate — campaigns at the top are losing list members fastest and deserve attention.

### Common variations

- **Subject line A/B testing analysis** — if the user runs A/B tests, surface which variants won and what patterns emerge.
- **Send-time analysis** — different audiences open at different times; surface the best send time by segment.
- **Content-to-conversion** — combine with CRM data: of subscribers who engaged with the newsletter regularly, what % became customers vs the non-engaged segment?

## Video content performance

### Chart choices

A grouped bar chart with the user's top 10 videos per platform works well for platform-by-platform review. For cross-platform: a scatter plot with views on the x-axis and completion rate on the y-axis, dots colored by platform and sized by engagement.

### Common variations

- **Hook analysis** — for short-form video specifically, the first 3 seconds determine retention. Pull only the top performers and surface what their hooks have in common.
- **Posting cadence vs performance** — does posting daily produce better aggregate results than posting weekly? Cross-reference posting frequency with average performance.
- **YouTube long-form vs Shorts** — separate analysis. Different audiences, different success criteria.

## Content gap analysis

### Chart choices

A horizontal bar chart of the top 30 gap keywords by monthly search volume works as the primary view. Color-code by suggested content type (informational, commercial, etc.) so the user can plan content programs by theme.

A secondary view: cumulative-volume curve — the top 10 gap keywords represent how much of the total gap volume? Often 70-80%, meaning a small number of content pieces can capture most of the opportunity.

### Common variations

- **Topic clustering** — instead of keyword-by-keyword, cluster the gaps into topic clusters and recommend pillar pages.
- **Quick wins** — surface keywords where the user already ranks page 2-3 but a content update could push to page 1. These are faster than net-new content.
- **Featured snippets and PAA** — if the data is available, surface queries where competitors win featured snippets or People Also Ask boxes. These are higher-leverage than position-only competitions.
