---
name: supermetrics-audits-and-monitors
description: >-
  Runs a one-off health audit of a Google Ads, Facebook Ads or Snapchat Ads account and sets up
  scheduled monitors that alert by email or Slack: metric thresholds (spend, CPA, conversions,
  sessions) on Google Ads, Facebook Ads and GA4, landing page checks for broken or redirecting
  URLs, and campaign naming convention checks. Audit findings are grouped by severity across
  tracking trust, delivery, waste, efficiency, allocation, change and upside. Use when the user
  asks to "audit my Google Ads account", "what's wrong with my Meta account", "alert me if spend
  spikes", "set up a daily check that conversions are still tracking", "check our ad landing
  pages work", or "flag campaigns that break our naming convention". Don't use for a one-off
  metric question (use supermetrics-marketing-data-queries) or a week-over-week performance
  review (use supermetrics-campaign-performance-review).
---

# Audits and monitors

Two tools: `manage_audits_and_monitors` starts audits and creates, updates or runs monitors; `read_audits_and_monitors` lists monitors, shows one monitor, returns a monitor type's schema and waits for audit reports. If the Supermetrics tools aren't connected or a source isn't signed in, follow the `supermetrics-get-started` skill first.

## Audit an account

One account at a time, Google Ads (`AW`), Facebook Ads (`FA`) or Snapchat Ads (`SCM`) only.

1. **Pick the account.** `accounts_discovery` for the platform. Ask which account if there are several, and ask the account's time zone if you don't know it; don't guess.
2. **Start.** `manage_audits_and_monitors` with `action: audit`, `ds_id`, `account_ids` (exactly one), `timezone`, and `date_range` (`last_7_days`, `last_14_days`, `last_30_days` default, `last_90_days`).
3. **Wait for the report.** `read_audits_and_monitors` with `action: audit` and the `run_token`. While it reports pending, call again with the token it returns.
4. **Present.** Lead with the most severe findings and what each one costs or risks. Keep the seven angles as headings only where they have findings.
5. **Follow up.** Use `supermetrics-marketing-data-queries` to dig into a finding; use `supermetrics-campaign-management` for any fix, with the user's approval per change.

The audit doesn't judge ad copy, creative, landing page relevance, search term intent or why numbers moved; say so if the user asks. For one campaign's health use `campaign_and_resource_get` (`resource_type: health_check`); for UTM and link checks use `marketing_utilities`.

## Create a monitor

Monitors run on a schedule and notify by email or Slack.

| Type | Platforms | Checks |
|---|---|---|
| `metrics` | Google Ads, Facebook Ads, GA4 | A metric crosses a threshold, optionally filtered by dimension (for example sessions from one channel) |
| `page_check` | Google Ads, Facebook Ads | Ad landing pages return the expected status code without too many redirects |
| `naming_convention` | Google Ads, Facebook Ads | Campaign or ad names follow the team's segments and delimiter |

1. **Clarify** what to watch, the threshold and direction, which accounts, how often (`hourly`, `every6hours`, `daily`, `weekly`), the time zone, and who gets notified. Check `get_business_context` for an existing naming convention or targets.
2. **Get the schema.** `read_audits_and_monitors` with `action: schema`, `monitor_type` and `ds_id`. Build the config from its example; take metric and dimension IDs from `field_discovery`, never from memory.
3. **Confirm.** A new monitor starts running straight away and sends notifications to the recipients you list. Show the user the name, accounts, rule, schedule and recipients, and get a yes. Pass `status: disabled` if they want to switch it on later.
4. **Create.** `manage_audits_and_monitors` with `action: create`, `name`, `ds_id`, `account_ids`, `config` and `schedule` (`time` for daily and weekly, `day_of_week` for weekly, `timezone` always).
5. **Offer a first run.** `action: run` with the `monitor_id` returns the result now. If it is still running, call again with the same `run_token` rather than starting a second run.
6. **Report** the monitor's name, ID, schedule and recipients.

## Change or review monitors

- `read_audits_and_monitors` with no arguments lists the user's monitors; `action: get` with `monitor_id` shows one, including its editable configuration.
- To update, always `get` first. `config`, `schedule` and `account_ids` replace the stored value whole, so send the complete value from `get` with only the user's change applied, keeping notifications and any `stored:…` references unchanged. Never build a config from a list result.
- To pause a monitor, update its `status` to `disabled`. `name`, `description`, `status` and `shared_with_team` can be changed on their own.
- When changing accounts, pass the monitor's current `connection` (from `get`) unless the user wants to switch login.
