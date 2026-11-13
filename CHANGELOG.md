# Changelog

Changes to the manifests and documentation in this repository.

This is not the changelog for the Supermetrics MCP server itself — the server is hosted and updates
continuously. For tool, data source and capability changes, see
[mcp.supermetrics.com/changelog](https://mcp.supermetrics.com/changelog).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this repository
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Meta reference (`supermetrics-marketing-data-queries`): Advantage+ placements is on only while `platforms`,
  `devices`, `placements` and `excluded_placements` are unset, and sending any of them turns it off; a local video file goes
  through `creative_picker` `create_upload_url` (with a video `content_type` and `size`) and its `upload_ref`.
- Meta reference: `placements` and `excluded_placements` no longer say they need `platforms`. While an ad set has no
  platform list, they set Facebook and Instagram positions and Meta's other default platforms still apply; sending
  `platforms` narrows delivery to the platforms named.

## [2.1.0] - 2026-11-10

### Added

- `supermetrics-social-media-publishing` skill: publishes and schedules Instagram, LinkedIn page and Google Business
  Profile posts, manages scheduled posts, replies to and moderates comments and reviews, and finds the best time to
  post. Every post needs the user's approval and a dry run (`validate_only`) first.
- `supermetrics-audits-and-monitors` skill: runs a one-off Google Ads or Meta Ads account audit, and creates, runs and
  updates scheduled monitors (metric thresholds on Google Ads, Meta Ads and GA4, landing page checks, naming
  convention checks) that alert by email or Slack.

## [2.0.0] - 2026-11-11

### Added

- `.gemini-plugin/plugin.json`, a plugin manifest in the Gemini plugin format, listing the skills and the server.
- CI checks every skill against the plugin directory rules: name equals its folder and starts with `supermetrics-`,
  description of at most 1,024 characters, `SKILL.md` within 500 lines and under 3,000 words, and the same version in
  every manifest.
- `live-dashboards` skill: plans, builds and publishes live Supermetrics Studio dashboards, including dashboards
  with campaign actions (pause or enable a campaign, change a budget). It covers when to offer a dashboard, how to
  plan it and what makes it useful, and defers the code details to the `manage_dashboards` tool instructions.
- Each skill has an `agents/openai.yaml` with a display name, short description, starter prompt and icon, so the
  skills show readable names in ChatGPT and Codex.

### Changed

- **Breaking:** every skill name now starts with `supermetrics-` (for example `campaign-management` is now
  `supermetrics-campaign-management`), as plugin directories require brand-prefixed skill names. Scripts or
  instructions that refer to a skill by its old name need updating.
- Skill descriptions now list the requests each skill is for, in quotes, and name the skill to use instead for
  nearby requests, so the right skill is picked more often.
- The four longest skills (`supermetrics-event-and-webinar-roi`, `supermetrics-ecommerce-roas-analysis`,
  `supermetrics-seo-and-content-performance`, `supermetrics-executive-marketing-report`) keep their workflow steps in
  `SKILL.md` and move the detailed output specifications to `references/`, read only when that workflow runs.
- TikTok reference (`marketing-data-queries`): states the minimum daily budget per currency (also for LIFETIME
  budgets), that `FOLLOWERS` is a goal billed as `OCPM`, that other billing with `CONVERT` or `FOLLOWERS` and a
  `REACH` ad group without `frequency` are refused, that `CUSTOMIZED_USER` identities are refused, and how to list
  identities (`asset_type='identity'`).
- Skills restructured into thirteen focused skills, one per job, so each is matched to the right request:
  `get-started`, `marketing-data-queries` (replaces `marketing-data-analysis` and the seven connector skills,
  which are now its per-platform references), `campaign-management`, `wasted-spend-audit`,
  `ad-creative-performance`, `campaign-performance-review`, `budget-pacing-allocation`,
  `ecommerce-roas-analysis`, `executive-marketing-report`, `funnel-and-tracking-audit`, `b2b-pipeline-analysis`,
  `seo-and-content-performance` and `event-and-webinar-roi`, with helper scripts and report templates.
- TikTok reference (`marketing-data-queries`): to track a `CLICK` ad group with a pixel, set it on each ad as
  `platform_settings.tracking_pixel_id` (the numeric pixel id); an ad group `pixel_id` with goal `CLICK` is sent
  to its new ads that way. On an existing ad the pixel changes only together with a creative change.
- TikTok reference (`marketing-data-queries`): `locations` takes country codes or TikTok numeric location ids
  (not city names or GeoNames ids), and `location_details` is not supported.
- Skill reference guides no longer name Claude-only features (artifacts, Recharts, `ask_user_input_v0`,
  `AskUserQuestion`); they describe the same steps in client-neutral terms, as the ChatGPT plugin directory requires.

### Fixed

- `tiktok-ads`: campaign writes name the values TikTok accepts — `objective_type` `WEB_CONVERSIONS`,
  `optimization_goal` `SHOW` and `CONVERT` (not `IMPRESSION`/`CONVERSION`), `pixel_event` examples
  (`SHOPPING`, `ON_WEB_CART`, `ON_WEB_REGISTER`) and the `billing_event` for each goal, including `OCPM`.
- Skills: campaign `targeting` updates are described as per-field (omitted fields are kept, a sent
  field replaces that list), matching the server, instead of "replaces, never merges". Same fix in
  the README and `GEMINI.md`.
- Skills: the Google Ads keyword note now points to the response notes, which name any keyword that
  wasn't added, instead of a blanket "verify in the UI".
- `tiktok-ads`: removed `engagement_rate` from standard-report examples; it exists only on Smart+
  report types and fails beside `ad_id`.
- `instagram-insights`: removed the non-existent `hour` dimension, and split `followers_count` out of
  the account-overview example, where it returned nulls.
- Skills: replaced stale per-connector field counts.

## [1.1.0] - 2026-09-28

### Added

- Seven connector skills: `google-ads`, `meta-ads`, `google-analytics-4`, `linkedin-ads`,
  `tiktok-ads`, `microsoft-ads` and `instagram-insights`.
- Bluesky Public Data in the README data source list.
- README: the `manage_dashboards` `list` action, `manage_user_and_team` log-out, and links to the
  health check and Swagger 2.0 specification.

### Changed

- Snapchat Ads added wherever campaign-write platforms are listed.
- Connector counts removed from the README, `llms.txt`, `GEMINI.md`, skills and every manifest
  description. The live count changes as connectors are added; the tools report the current list.

## [1.0.0] - 2026-09-10

### Added

- `server.json` for the official [MCP Registry](https://registry.modelcontextprotocol.io/), declaring
  `com.supermetrics/mcp` as a remote `streamable-http` server.
- Plugin manifests for Claude Code (`.claude-plugin/`), Codex (`.codex-plugin/`) and Cursor
  (`.cursor-plugin/`).
- Client configurations for VS Code (`.vscode/mcp.json`), Gemini CLI (`gemini-extension.json` with
  `GEMINI.md`) and generic MCP clients (`.mcp.json`).
- Two bundled skills: `marketing-data-analysis` and `campaign-management`.
- One-click install badges for Cursor and VS Code.
- Continuous validation workflow: JSON parse checks, required-manifest checks, server URL
  consistency, and a daily endpoint health check.
- Issue templates routing account and billing questions to Supermetrics support and product ideas to
  the public wishlist.

[2.1.0]: https://github.com/supermetrics-public/supermetrics-mcp/releases/tag/v2.1.0
[2.0.0]: https://github.com/supermetrics-public/supermetrics-mcp/releases/tag/v2.0.0
[1.1.0]: https://github.com/supermetrics-public/supermetrics-mcp/compare/v1.0.0...main
[1.0.0]: https://github.com/supermetrics-public/supermetrics-mcp/releases/tag/v1.0.0
