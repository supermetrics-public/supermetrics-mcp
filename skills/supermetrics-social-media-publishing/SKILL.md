---
name: supermetrics-social-media-publishing
description: >-
  Publishes and schedules organic social posts and manages comments through Supermetrics on
  Instagram professional accounts, LinkedIn company pages and Google Business Profile: images,
  carousels, reels, stories, text and link posts; listing, rescheduling and cancelling scheduled
  posts; replying to, hiding or deleting comments; answering Google reviews; and finding the best
  time to post. Every post needs the user's explicit approval and a dry run first. Use when the
  user asks to "schedule an Instagram post for Friday", "post this on our LinkedIn page", "reply
  to yesterday's comments", "what's scheduled this week", or "when is the best time to post".
  Don't use for analysing organic content performance (use
  supermetrics-seo-and-content-performance) or for paid ads and boosting (use
  supermetrics-campaign-management).
---

# Social media publishing and comments

Publish, schedule and moderate posts with the `manage_social_media` tool. Posts go out under the user's brand, so treat every write as public and permanent. If the Supermetrics tools aren't connected or a source isn't signed in, follow the `supermetrics-get-started` skill first.

## Platforms

| Platform | `ds_id` | Content types |
|---|---|---|
| Instagram (professional account) | `IGI` (default) | `IMAGE`, `VIDEO` (a reel), `CAROUSEL` (2–10), `STORY` (no caption; `frames` for a sequence) |
| LinkedIn company page | `LIP` | `TEXT`, `LINK`, `IMAGE`, `CAROUSEL` (2–20 images); no video or stories |
| Google Business Profile | `GMB` | `TEXT`, `IMAGE` (one image); caption required, up to 1,500 characters |

Get `account_id` from `accounts_discovery` with the same `ds_id`; for Google Business Profile pick a single location. Before the first publish, call `supermetrics_guide` with `mode: social_publishing` for the exact limits and `platform_options` of each type (Instagram user tags and collaborators, Google event, offer and call-to-action posts).

## Workflow

1. **Resolve the account.** `accounts_discovery` for the platform; confirm which account if there are several.
2. **Read the house rules.** `get_business_context` with `ds_id` and `account_id` for brand voice, hashtag and posting rules, and apply them to the draft.
3. **Draft and confirm.** Show the user the final caption, media, account and time with time zone. Wait for a clear yes before any write.
4. **Dry run.** Send the exact call with `validate_only: true`. It checks parameters, media, permissions, write access and quota without posting. Fix anything it reports.
5. **Publish or schedule.** Repeat the call without `validate_only`.
6. **Verify.** Report what was published or scheduled, with the post link or `scheduled_id`. Follow the result's `next_action` if it has one.

## Access and approval

- Writes must be enabled per account at [hub.supermetrics.com/write-settings](https://hub.supermetrics.com/write-settings), where the account owner also sets the approval level. A `WRITE_ACCESS_NOT_ENABLED` error includes the link to give the user; only the person who connected the account can turn it on.
- `SCOPE_UPGRADE_REQUIRED` means the connection was made without publishing or comment permissions. Give the user the reconnect link from the error (or `get_user_and_team` with `action: login_data_source` and `include_management_scopes: true`) and stop.
- If the account requires approval, the write returns an approval link. Show it to the user and stop. After they approve, call `status` with `approval_ref` for the result.

## Safety rules

- **Never publish or schedule without explicit approval** of the final content and time.
- **Published posts can't be edited or deleted here.** The user does that in the platform. Only posts scheduled through Supermetrics can be changed or cancelled.
- **Deleting a comment is irreversible.** Confirm before `moderate_comment` with `delete: true`; on Instagram prefer `hidden: true` when the user is unsure.
- **Don't retry blindly.** Repeating the same publish or comment within 15 minutes returns the first result instead of posting twice; retry only failures the result marks as safe to retry.

## Publishing

Use `action: publish` with `content_type`, `caption` and one media source:

- `media_url` (a public https URL), or `upload_ref` from `creative_picker`, which browses, uploads or AI-generates images with a preview. Use `media_prompt` (an AI image without preview) only if the user says they don't need to review it.
- Carousels take several: `media_urls`, `media_prompts` or a comma-separated `upload_ref`.
- AI images, videos and story sequences run in the background: poll `status` with `job_id` and `wait_seconds`.

## Scheduling

- Add `publish_at`, 5 minutes to 75 days ahead. Without a UTC offset, also send `timezone` (IANA, for example `Europe/Helsinki`); ask the user if unsure.
- `status` with no target lists scheduled posts (`state`: `scheduled`, `published`, `failed`, `cancelled`, `all`). Change caption or time with `update_post` and `scheduled_id`; to change media, `cancel_scheduled` and publish again.
- Space one account's posts at least 5 minutes apart; Instagram throttles bursts.
- **Best time to post (Instagram).** Run `data_query` with `ds_id: IGI`, fields `date,hour,online_followers`, the last 30 days and `timezone` set to the user's zone, then suggest the hours with the most followers online. This needs an account with 100+ followers, and the latest 1–2 days may be missing; if it returns nothing, say so instead of guessing.

## Comments and reviews

- `list_comments` with `media_id`. Find recent post IDs with `instagram_insights` or `data_query` (ds_id `IGI`), or the share or post IDs for `LIP`.
- Reply with `action: comment` and `comment_id`; hide, unhide or delete with `moderate_comment`; turn comments on or off with `update_post` and `media_id`.
- LinkedIn can't hide comments, threads replies one level deep and shows commenters as LinkedIn IDs, not names.
- Google Business Profile: `list_comments` without `media_id` lists the location's reviews with their rating. A reply replaces the previous one; deleting removes only your reply, not the review.

## After publishing

Analyse results later with `instagram_insights` or `data_query` (`IGI` for Instagram, `LIP` for LinkedIn page posts, `GMB` for Google Business Profile), following the `supermetrics-marketing-data-queries` skill.
