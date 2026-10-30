# Changelog

Changes to the manifests and documentation in this repository.

This is not the changelog for the Supermetrics MCP server itself — the server is hosted and updates
continuously. For tool, data source and capability changes, see
[mcp.supermetrics.com/changelog](https://mcp.supermetrics.com/changelog).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this repository
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `live-dashboards` skill: plans, builds and publishes live Supermetrics Studio dashboards, including dashboards
  with campaign actions (pause or enable a campaign, change a budget). It covers when to offer a dashboard, how to
  plan it and what makes it useful, and defers the code details to the `manage_dashboards` tool instructions.
- Each skill has an `agents/openai.yaml` with a display name, short description, starter prompt and icon, so the
  skills show readable names in ChatGPT and Codex.

### Changed

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

[1.1.0]: https://github.com/supermetrics-public/supermetrics-mcp/compare/v1.0.0...main
[1.0.0]: https://github.com/supermetrics-public/supermetrics-mcp/releases/tag/v1.0.0
