# Changelog

Changes to the manifests and documentation in this repository.

This is not the changelog for the Supermetrics MCP server itself — the server is hosted and updates
continuously. For tool, data source and capability changes, see
[mcp.supermetrics.com/changelog](https://mcp.supermetrics.com/changelog).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this repository
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
