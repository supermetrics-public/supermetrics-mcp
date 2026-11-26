---
name: supermetrics-audits-and-monitors
description: >-
  Runs a health audit of a Google Ads, Facebook Ads, Snapchat Marketing, LinkedIn Ads, TikTok Ads
  or Microsoft Ads account over the last 7–90 days, showing how it performs and what's wrong, and
  sets up scheduled monitors that alert by email or Slack: metric thresholds on Google Ads,
  Facebook Ads, Microsoft Ads, LinkedIn Ads, DV360 and Google Analytics 4, landing page checks for
  broken or redirecting URLs, and campaign naming convention checks. Findings are grouped by
  severity across tracking, delivery, waste, efficiency, allocation, change and upside. Use when
  asked to "audit my Google Ads account", "how is my Google Ads account performing", "what's wrong
  with my Meta account", "alert me if spend spikes", "set up a daily check that conversions are
  still tracking", "check our ad landing pages work", or "flag campaigns that break our naming
  convention". Not for one-off metric questions (use supermetrics-marketing-data-queries) or a
  week-over-week performance review (use supermetrics-campaign-performance-review).
---

# Audits and monitors

Two tools: `manage_audits_and_monitors` starts audits and creates, updates or runs monitors; `read_audits_and_monitors` lists monitors, shows one monitor, returns a monitor type's schema and waits for audit reports. If the Supermetrics tools aren't connected or a source isn't signed in, follow the `supermetrics-get-started` skill first.

## Audit an account

One account at a time, Google Ads (`AW`), Facebook Ads (`FA`), Snapchat Marketing (`SCM`), LinkedIn Ads (`LIA`), TikTok Ads (`TIK`) or Microsoft Ads (`AC`) only.
For LinkedIn Ads, the conversion checks leave out the last 7 days because LinkedIn conversions arrive late, so `last_7_days` gives no conversion verdicts; suggest `last_30_days`.

1. **Pick the account.** `accounts_discovery` for the platform. Ask which account if there are several, and ask the account's time zone if you don't know it; don't guess. LinkedIn Ads reports days in UTC: pass `timezone: UTC` without asking.
2. **Start.** `manage_audits_and_monitors` with `action: audit`, `ds_id`, `account_ids` (exactly one), `timezone`, and `date_range` (`last_7_days`, `last_14_days`, `last_30_days` default, `last_90_days`).
3. **Wait for the report.** `read_audits_and_monitors` with `action: audit` and the `run_token`. While it reports pending, call again with the token it returns.
4. **Present.** Lead with the most severe findings and what each one costs or risks. Keep the seven angles as headings only where they have findings.
5. **Follow up.** Use `supermetrics-marketing-data-queries` to dig into a finding; use `supermetrics-campaign-management` for any fix, with the user's approval per change.

The audit doesn't judge ad copy, creative, landing page relevance, search term intent or why numbers moved; say so if the user asks. For one campaign's health use `campaign_and_resource_get` (`resource_type: health_check`, which requires `params.campaign_id`); for UTM and link checks use `marketing_utilities`.

## Create a monitor

Monitors run on a schedule and notify by email or Slack.

| Type | Platforms | Checks |
|---|---|---|
| `metrics` | Google Ads, Facebook Ads, Microsoft Ads, LinkedIn Ads, Google Analytics 4, Google Display & Video 360 | A metric crosses a threshold, optionally filtered by dimension (for example sessions from one channel) |
| `page_check` | Google Ads, Facebook Ads, Microsoft Ads, LinkedIn Ads | Ad landing pages return the expected status code without too many redirects |
| `naming_convention` | Google Ads, Facebook Ads, Microsoft Ads, LinkedIn Ads, Google Display & Video 360 | Campaign or ad names follow the team's segments and delimiter |

1. **Pick the type and platform**, then get the schema: `read_audits_and_monitors` with `action: schema`, `monitor_type` and `ds_id`. Allowed values for entity, url_field and date_range differ by ds_id and monitor_type: use the schema for that exact pair, not values from another source or type.
2. **Clarify** what to watch, the threshold and direction, over which period (one of the schema's `date_ranges`), how often (one of its `schedule_frequencies`), which accounts, the time zone, and who gets notified. Check `get_business_context` for an existing naming convention or targets.
3. **Build the config** from the schema's example; take metric and dimension IDs from `field_discovery`, never from memory.
4. **Confirm.** A new monitor starts running straight away and sends notifications to the recipients you list. Show the user the name, accounts, rule, schedule and recipients, and get a yes. Pass `status: disabled` if they want to switch it on later.
5. **Create.** `manage_audits_and_monitors` with `action: create`, `name`, `ds_id`, `account_ids`, `config` and `schedule` (`time` for daily and weekly, `day_of_week` for weekly, `timezone` always).
6. **Offer a first run.** `action: run` with the `monitor_id` returns the result now. If it is still running, call again with the same `run_token` rather than starting a second run.
7. **Report** the monitor's name, ID, schedule and recipients.

## Change or review monitors

- `read_audits_and_monitors` with no arguments lists the user's monitors; `action: get` with `monitor_id` shows one, including its editable configuration.
- To update, always `get` first. `config`, `schedule` and `account_ids` replace the stored value whole, so send the complete value from `get` with only the user's change applied, keeping notifications and any `stored:…` references unchanged. Never build a config from a list result.
- To pause a monitor, update its `status` to `disabled`. `name`, `description`, `status` and `shared_with_team` can be changed on their own.
- When changing accounts, pass the monitor's current `connection` (from `get`) unless the user wants to switch login.
