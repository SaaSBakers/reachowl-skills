---
name: instagram-dm-automation
description: "Instagram DM automation with ReachOwl: send personalized direct messages to Instagram followers, following, hashtag and location audiences, and post commenters, with follow + DM sequences and daily limits. Use when the user wants to DM a competitor's followers, message people who commented on a post, reach a hashtag or location audience, upload a lead list for Instagram outreach, or automate Instagram outreach."
license: MIT
compatibility: Requires the ReachOwl MCP server (https://reachowl.com/mcp) and an Instagram account connected in ReachOwl.
metadata:
  author: ReachOwl
  version: "1.2.0"
  homepage: https://reachowl.com
  tags: instagram-dm, instagram-automation, dm-automation, instagram-marketing, cold-outreach, lead-generation, reachowl
---

# Instagram DM Automation (ReachOwl)

Send personalized Instagram DMs to the right audience from the user's own connected Instagram account. A ReachOwl campaign pulls the audience (an account's followers or following, a hashtag, a location, a post's commenters, or an uploaded list) and works through it at a safe daily limit.

For Facebook, use `facebook-dm-automation`. For WhatsApp, use `whatsapp-marketing-automation`.

## Before you start

- Requires the ReachOwl MCP server connected with the user's token. If a tool reports a missing token, run the `reachowl-auth` flow.
- Confirm the audience, message text, and daily limit with the user before switching a campaign on (`status: 1` starts sending).

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_campaigns` | `{ platform: "instagram", page?, per_page? }` — omit `team_id` |
| `get_campaign` | `{ id }` |
| `create_campaign` | `{ body }` — create a **new** campaign only |
| `update_campaign` | `{ id, body }` — partial; `{ status: 1 }` start, `{ status: 0 }` pause |
| `delete_campaign` | `{ id }` |
| `add_campaign_contacts` | `{ campaign_id, audience: [...] }` — upload leads to an **existing** campaign (max 500 rows per call) |
| `list_app_states` | Connected accounts → pick an `instagram` one for `executor_ids` |

## Never

- Never call `create_campaign` to upload contacts. "Add these leads to campaign 18312" → `add_campaign_contacts`.

## Choose the audience

| Who to message | `audience_type` | `post_url` | `action_type` |
|----------------|-----------------|------------|---------------|
| An account's followers (e.g. a competitor) | `followers` | Username or profile URL | `messages` or `friend_request` (follow) |
| Accounts someone follows | `following` | Username or profile URL | `messages` or `friend_request` (follow) |
| People who commented on a post | `comments` | `https://www.instagram.com/p/…` | `messages` (or `comments` to reply under their comment) |
| People posting with a hashtag | `hashtag` | The hashtag, e.g. `realestatepakistan` | `messages` or `friend_request` (follow) |
| People posting from a location | `location` | Instagram location URL | `messages` or `friend_request` (follow) |
| A list of profiles (CSV, sheet, scrape) | `file_upload` | none; add contacts after create | `messages` |

To clean up the user's own following, use `action_type: "unfollow_friend"` with `filter_only: 1` and `quick_flow: "filter"`.

## Create a campaign

1. `get_user` → `team_id`.
2. `list_app_states` → the Instagram account ids → `executor_ids` (and `executor_id` = first id).
3. `create_campaign { body }`, created paused:

```json
{
  "body": {
    "name": "Competitor followers DM",
    "team_id": 7715,
    "platform": "instagram",
    "audience_type": "followers",
    "action_type": "messages",
    "post_url": "competitor.brand",
    "executor_id": 55,
    "executor_ids": [55],
    "limit": 25,
    "min_followers": 200,
    "status": 0,
    "messages": [
      { "text": ["Hey {{first_name}}! Loved your recent posts — quick question about your content workflow?", "Hi {{first_name}} 👋 mind if I share something that could help your page?"], "delay": 0 },
      { "text": ["Following up, {{first_name}} — happy to send a quick example if useful."], "delay": 2880 }
    ]
  }
}
```

4. Show the user a summary (audience, messages, daily limit). On approval: `update_campaign { id, body: { status: 1 } }`.

**Messages**
- `messages[]` are sequence steps; `delay` is minutes after the previous step. Each `text[]` holds spin variants (one is picked per person).
- Placeholders: `{{name}}`, `{{first_name}}`, plus any custom key you put in contact `variables`.

**Follow + DM:** `action_type: "friend_request"` (follow) plus `friend_msg_sequence`: `0` = follow only, `1` = send the `messages` sequence when following, `2` = send it once they follow back. Add `new_conversation: 1` to skip people the user already has a conversation with.

**Useful filters:** `limit` (daily cap), `min_followers` / `min_followings`, `audience_keywords` / `exclude_keywords` (bio filters), `comment_keywords`, `countries`, `skip_duplicate`, `schedule: [{ day, start_time, end_time }]`, `clone_from`.

## Add a lead list to an existing campaign

```json
{
  "campaign_id": 18312,
  "audience": [
    { "name": "Sara Ahmed", "profile_url": "https://www.instagram.com/sara.example", "niche": "fitness" }
  ]
}
```

- Each row needs `profile_url` and/or `member_id` (the username); rows with neither are skipped.
- Extra fields (like `niche`) become message variables (`{{niche}}`).
- Send up to 500 rows per call. Verify with `list_contacts { campaign_id }`.

## Good practice

- Instagram is strict on new or recently connected accounts: start at 15–25 DMs a day and raise it slowly.
- Use `min_followers` and bio keywords to skip bots and inactive accounts.
- Mention something specific to the person and give 2–3 variants per step.
