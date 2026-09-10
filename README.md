# Supermetrics MCP

[![Install in Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=supermetrics&config=eyJ1cmwiOiJodHRwczovL21jcC5zdXBlcm1ldHJpY3MuY29tL21jcCJ9)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-install_Supermetrics_MCP-0098FF?logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=supermetrics&config=%7B%22type%22%3A%20%22http%22%2C%20%22url%22%3A%20%22https%3A%2F%2Fmcp.supermetrics.com%2Fmcp%22%7D)
[![MCP](https://img.shields.io/badge/Model_Context_Protocol-streamable_HTTP-black)](https://modelcontextprotocol.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Connect your AI agent to Supermetrics marketing data. Query live performance across 174 advertising, analytics, e-commerce and CRM platforms, explore metrics and dimensions, manage ad campaigns, and publish live dashboards — directly from your MCP-compatible client.

With the Supermetrics MCP server, you can:

- **Query live marketing data** from 174 platforms including Google Ads, Meta Ads, LinkedIn Ads, Google Analytics 4, TikTok Ads, Microsoft Advertising, Shopify, HubSpot and Salesforce.
- **Discover what's available** — browse data sources, accounts, fields, metrics and dimensions before you write a query.
- **Create and update ad campaigns** across Google, Meta, Microsoft, TikTok, LinkedIn and ChatGPT Ads, including ad groups, ads and creatives.
- **Publish live, shareable dashboards** to Supermetrics Studio that re-query your data on every view.
- **Keep your team's reporting conventions** — store business context once and have it applied consistently across sessions.

No pre-existing Supermetrics account is needed. A Supermetrics account with a 14-day free trial is created automatically on first login — no credit card required.

## What you can ask

Once the server is connected, these are ordinary questions — no query language, no report builder:

- "What did we spend on Google Ads last month, and what was the ROAS?"
- "Compare Meta Ads and TikTok Ads performance for Q3 by CPA."
- "Which Google Ads campaigns have a CPA above 50 EUR over the last 30 days?"
- "Show me GA4 sessions and conversions by channel, week over week."
- "Pull LinkedIn Ads leads by campaign and put them next to HubSpot deal stages."
- "How did Shopify revenue track against Meta Ads spend last quarter?"
- "Which Search Console queries lost the most clicks since August?"
- "Break down Amazon Ads ACOS by campaign for the last 90 days."
- "Pause every Microsoft Advertising ad group that spent more than 500 EUR with no conversions."
- "Draft a new Google Ads search campaign for our spring promo, but leave it paused."
- "Build me a live dashboard of blended CAC across Google, Meta and LinkedIn and give me the link."

The agent works out which data source, account, fields and date range it needs, runs the query against
your live accounts, and answers with real numbers.

## Server URL

| Endpoint | URL |
|----------|-----|
| MCP server | `https://mcp.supermetrics.com/mcp` |

## Ready-made connectors

Supermetrics is already listed in the following directories — connect there in one click:

- [Claude](https://claude.ai/directory/cc599e7b-8c59-4e89-9bf0-36d47bb9ec80)
- [ChatGPT](https://chatgpt.com/apps/supermetrics/asdk_app_69a1247fceb88191a0fde719fd50920d)
- [Google Gemini Enterprise](https://cloud.withgoogle.com/agentfinder/product/e978e9d5-2895-41e7-98a3-67e39af1cc45)
- [Microsoft Copilot](https://m365.cloud.microsoft/chat/?titleId=P_482b5243-c377-5a5d-d187-63349eb2d106)

## Installation

### Claude

Open **Settings → Connectors**, find **Supermetrics** in the Connectors Directory, and connect your account.

### ChatGPT

Find **Supermetrics** in the ChatGPT app directory and connect your account.

### Claude Code

Add Supermetrics as a remote HTTP server:

```bash
claude mcp add --transport http supermetrics https://mcp.supermetrics.com/mcp
```

Or install the plugin, which bundles the server together with the two
[skills](#bundled-skills) below:

```text
/plugin marketplace add supermetrics-public/supermetrics-mcp
/plugin install supermetrics
```

### Codex

Add the Supermetrics marketplace:

```text
codex plugin marketplace add supermetrics-public/supermetrics-mcp
```

Then run `/plugins` in Codex and install `supermetrics`. The plugin includes the server and both
[bundled skills](#bundled-skills).

### Cursor

Open **Settings → MCP** and add:

```json
{
  "mcpServers": {
    "supermetrics": {
      "type": "http",
      "url": "https://mcp.supermetrics.com/mcp"
    }
  }
}
```

### VS Code (Copilot agent mode)

Use the install badge at the top of this page, or add the server to VS Code's top-level `servers`
configuration. This repository also ships a ready-made `.vscode/mcp.json` — open the repo in VS Code
and it is picked up automatically.

```json
{
  "servers": {
    "supermetrics": {
      "type": "http",
      "url": "https://mcp.supermetrics.com/mcp"
    }
  }
}
```

### Zed

Add the server to Zed's `context_servers` settings:

```json
{
  "context_servers": {
    "supermetrics": {
      "url": "https://mcp.supermetrics.com/mcp"
    }
  }
}
```

### Gemini CLI

Install as a Gemini CLI extension:

```bash
gemini extensions install https://github.com/supermetrics-public/supermetrics-mcp
```

The extension ships a `GEMINI.md` context file so Gemini knows the discovery-then-query workflow
before its first call.

### Other clients

Supermetrics MCP works with any MCP-compatible client, including Windsurf, Cline, n8n, Microsoft Power Automate, Azure Logic Apps and Power Apps. Point the client at `https://mcp.supermetrics.com/mcp` over streamable HTTP.

## Authentication

Supermetrics uses OAuth 2.0. Your MCP client sends you to Supermetrics to sign in on first use. Dynamic Client Registration (DCR) and Client ID Metadata Document (CIMD) are both supported, so no pre-registered client ID is required.

| Purpose | Endpoint |
|---------|----------|
| Authorization | `https://api.supermetrics.com/oauth/authorize` |
| Token / refresh | `https://api.supermetrics.com/oauth/token` |

For server-to-server use, an API key can be sent as a bearer token instead. Create one at [hub.supermetrics.com/api-key-management](https://hub.supermetrics.com/api-key-management).

## Available tools

| Tool | Description |
|------|-------------|
| `supermetrics_guide` | Explains what the server can do, what's new, and how to resolve common setup and data issues. |
| `data_source_discovery` | Lists available data sources with auth status, or returns one source's full configuration. |
| `accounts_discovery` | Discovers connected accounts for a data source, with saved business context where present. |
| `field_discovery` | Explores available fields, metrics and dimensions for a data source. |
| `data_query` | Runs a query against a data source with fields, filters, date ranges and comparisons. |
| `get_async_query_results` | Retrieves results of a query by `schedule_id`. |
| `get_today` | Returns the current date, for resolving relative date references in queries. |
| `manage_business_context` | Stores and retrieves team guidelines and reporting preferences, scoped team-wide, per data source, or per account. |
| `campaign_and_resource_get` *(Beta)* | Lists campaigns and explores related resources — keywords, audiences, assets, recommendations and change history. |
| `manage_campaign` *(Beta)* | Creates or updates campaigns, ad groups and ads across Google, Meta, Microsoft, TikTok, LinkedIn and ChatGPT Ads. |
| `manage_dashboards` *(Beta)* | Uploads, reads and edits live, shareable dashboards in Supermetrics Studio. |
| `resources_manage` *(Beta)* | Browses, uploads or AI-generates ad creatives for use in campaigns. |
| `manage_user_and_team` | Returns profile, license and team info; invites members; issues data source login links. |
| `contact_supermetrics` | Sends product feedback, or creates a support ticket or sales enquiry. |

Campaign write actions must be enabled per advertising account at [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings). Every edit is logged at [hub.supermetrics.com/campaign-history](https://hub.supermetrics.com/campaign-history).

## Bundled skills

Plugin installs (Claude Code, Codex) also install two skills. A skill is loaded only when the model
decides it is relevant, so it costs nothing until it is needed — and it means the agent knows how to
use these tools well on its very first attempt instead of learning by trial and error.

| Skill | What it covers |
|-------|----------------|
| `marketing-data-analysis` | The discovery-then-query workflow, date ranges and period comparisons, filter syntax, how to read the 2D result array correctly, and how to interpret numbers rather than just restate them. |
| `campaign-management` | Safe campaign writes: read before write, create paused, per-platform creative rules for Google, Meta, Microsoft, TikTok and LinkedIn, and how to check `write_status` instead of blindly retrying. |

Clients without a plugin system get the same server and tools — the skills are simply not preloaded.

## Data sources

174 platforms, all reachable through the same tools and the same query shape. Supermetrics maintains
every connector, so schema changes and API deprecations upstream are handled for you.

Call `data_source_discovery` for the live list with authentication status, or see
[mcp.supermetrics.com/datasources](https://mcp.supermetrics.com/datasources).

**Advertising and media buying** (66)

AdRoll, Adform, Adthena, Amazon Ads, Amazon DSP, Apple Search Ads, Axon by AppLovin, Basis, Beeswax, Capterra PPC, Celtra, ChatGPT Ads, Criteo, Criteo Retail Media, DoubleVerify, Eskimi, Facebook Ads, Facebook Billing Data, Flashtalking, Google Ad Manager, Google AdSense, Google Ads, Google Ads Account Explorer, Google Ads Keyword Planner, Google Campaign Manager 360, Google Display & Video 360, Google Search Ads 360, IQM, Ignite, Integral Ad Science, Kwai Ads, LINE Ads, LY Ads Display Ads, LY Ads Search Ads, Lazada Ads, Liftoff, LinkedIn Ads, LiveIntent, MNTN, Microsoft Advertising (Bing), Moloco DSP, Nexxen DSP, Nielsen Digital Ad Ratings, Outbrain Amplify, Outbrain DSP (Zemanta), Pinterest Ads, Quantcast, Quora Ads, RTB House, Readpeak, Reddit Ads, Shopee Ads, Snapchat Marketing, Spotify Ads, StackAdapt, Taboola, Teads, The Trade Desk, TikTok Ads, Vibe, Walmart Connect (Display), Walmart Connect (Search), X Ads (Twitter), Xing Ads, Yahoo DSP, Yandex.Direct

**Social and organic** (21)

Apple Public Data, Bambuser, Facebook Insights, Facebook Public Data, Google My Business, Instagram Insights, Instagram Public Data, LinkedIn Company Pages, Meltwater, Pinterest Organic, Pinterest Public Data, Slack, Smarp, Sprinklr, Sprout Social, Threads Insights, TikTok Organic, Vimeo Public Data, X Organic (Twitter), YouTube, YouTube Public Data

**Web, product and app analytics** (15)

Adjust, Adobe Analytics, Amplitude, AppsFlyer, Branch, Google Analytics 4, Google PageSpeed Insights, Google Play Console, Hotjar, Matomo, Mixpanel, Piano Analytics (AT Internet), Piwik PRO, Plausible, Yandex.Metrica

**SEO and competitive intelligence** (8)

Ahrefs, Bing Webmaster Tools, Google Search Console, Google Trends, Semrush Analytics, Semrush Projects, Similarweb, Yext

**E-commerce and retail** (19)

Adobe Commerce (Magento 2), Amazon Seller Central, Amazon Vendor Central, BigCommerce, Centra, Ecwid, Google Merchant Center, Lazada Commerce, PrestaShop, Prisjakt, Recharge, Shopee Commerce, Shopify, Shopware, Squarespace Commerce, Stripe, TikTok Shop, Wix Commerce, WooCommerce

**CRM, email and marketing automation** (21)

ActiveCampaign, Braze, Brevo, CallRail, Campaign Monitor, Close CRM, Eloqua, Gong, HubSpot, HubSpot Contacts, HubSpot Content Analytics, HubSpot Marketing Emails, HubSpot Marketing Forms, Klaviyo, MailChimp, Marketo, Odoo CRM, Omnisend, Pipedrive, Salesforce, Zoho CRM

**Affiliate and partner** (9)

Adtraction, Affluent, Awin, CJ Affiliate, Everflow, Impact, Partnerize, Rakuten Advertising, Tradedoubler

**Reviews and reputation** (8)

Capterra Reviews, G2 Reviews, Glassdoor Reviews, Google Play Reviews, Indeed Reviews, Simplesat, Tripadvisor Reviews, Yelp Reviews

**Warehouses, spreadsheets and utilities** (7)

Clockify, Data Blending, Google BigQuery, Google Sheets, Harvest, Snowflake, Snowflake (Legacy)

## Resources

- [Server documentation](https://mcp.supermetrics.com/docs)
- [Full data source list](https://mcp.supermetrics.com/datasources)
- [Server changelog](https://mcp.supermetrics.com/changelog) · [repository changelog](CHANGELOG.md)
- [OpenAPI specification](https://mcp.supermetrics.com/openapi.json)
- [LLM summary](https://mcp.supermetrics.com/llms.txt) · [full reference](https://mcp.supermetrics.com/llms-full.txt)
- [Supermetrics Hub](https://hub.supermetrics.com/)
- [Knowledge base](https://docs.supermetrics.com/)
- [Model Context Protocol](https://modelcontextprotocol.io/)

## License

This repository is available under the [MIT License](LICENSE).

## Security

See [SECURITY.md](SECURITY.md) and the [Supermetrics privacy policy](https://supermetrics.com/privacy-policy).

## Support

- **Issues**: [GitHub Issues](https://github.com/supermetrics-public/supermetrics-mcp/issues)
- **Knowledge base**: [docs.supermetrics.com](https://docs.supermetrics.com/)
- **Privacy**: [Supermetrics privacy policy](https://supermetrics.com/privacy-policy)
- **Terms**: [Terms of service and DPA](https://supermetrics.com/terms-of-service)
