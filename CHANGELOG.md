# Changelog

Changes to the manifests and documentation in this repository.

This is not the changelog for the Supermetrics MCP server itself — the server is hosted and updates
continuously. For tool, data source and capability changes, see
[mcp.supermetrics.com/changelog](https://mcp.supermetrics.com/changelog).

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this repository
adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-09-10

### Added

- `server.json` for the official [MCP Registry](https://registry.modelcontextprotocol.io/), declaring
  `com.supermetrics/mcp` as a remote `streamable-http` server.
- Plugin manifests for Claude Code (`.claude-plugin/`), Codex (`.codex-plugin/`), Cursor
  (`.cursor-plugin/`) and Google Antigravity (`.agents/plugins/`, `plugin.json`, `mcp_config.json`).
- Client configurations for VS Code (`.vscode/mcp.json`), Gemini CLI (`gemini-extension.json` with
  `GEMINI.md`) and generic MCP clients (`.mcp.json`, `mcp_config.json`).
- Two bundled skills: `marketing-data-analysis` and `campaign-management`.
- One-click install badges for Cursor and VS Code.
- Continuous validation workflow: JSON parse checks, required-manifest checks, server URL
  consistency, and a daily endpoint health check.
- Issue templates routing account and billing questions to Supermetrics support and product ideas to
  the public wishlist.

[1.0.0]: https://github.com/supermetrics/supermetrics-mcp/releases/tag/v1.0.0
