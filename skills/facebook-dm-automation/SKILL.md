---
name: facebook-dm-automation
description: "Facebook DM automation with ReachOwl: send personalized direct messages and friend requests to Facebook group members, post commenters, reactors, and friends, with follow-up sequences and daily limits. Use when the user wants to DM a Facebook group, message people who commented on or reacted to a post, run friend request + DM campaigns, upload a lead list for Facebook outreach, or automate Facebook outreach."
license: MIT
compatibility: Requires the ReachOwl MCP server (https://reachowl.com/mcp) and a Facebook account connected in ReachOwl.
metadata:
  author: ReachOwl
  version: "1.2.0"
  homepage: https://reachowl.com
  tags: facebook-dm, facebook-automation, dm-automation, facebook-marketing, cold-outreach, lead-generation, reachowl
---

# Facebook DM Automation (ReachOwl)

Send personalized Facebook DMs and friend requests to the right audience from the user's own connected Facebook account. A ReachOwl campaign pulls the audience (group members, post commenters, reactors, friends, or an uploaded list) and works through it at a safe daily limit.

For Instagram, use `instagram-dm-automation`. For WhatsApp, use `whatsapp-marketing-automation`.

## Before you start

- Requires the ReachOwl MCP server connected with the user's token. If a tool reports a missing token, run the `reachowl-auth` flow.
- Confirm the audience, message text, and daily limit with the user before switching a campaign on (`status: 1` starts sending).

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_campaigns` | `{ platform: "facebook", page?, per_page? }` — omit `team_id` |
| `get_campaign` | `{ id }` |
| `create_campaign` | `{ body }` — create a **new** campaign only |
| `update_campaign` | `{ id, body }` — partial; `{ status: 1 }` start, `{ status: 0 }` pause |
| `delete_campaign` | `{ id }` |
| `add_campaign_contacts` | `{ campaign_id, audience: [...] }` — upload leads to an **existing** campaign (max 500 rows per call) |
| `list_app_states` | Connected accounts → pick a `facebook` one for `executor_ids` |
| `list_groups` | Facebook groups synced in the user's account |

## Never

- Never call `create_campaign` to upload contacts. "Add these leads to campaign 18312" → `add_campaign_contacts`.

## Choose the audience

| Who to message | `audience_type` | `post_url` | `action_type` |
|----------------|-----------------|------------|---------------|
| Members of a Facebook group | `group` | `https://www.facebook.com/groups/…` | `messages` or `friend_request` |
| People who commented on a post or ad | `comments` | Full post or ad URL | `messages` (or `comments` to reply under their comment) |
| People who reacted to a post | `reactions` | Full post URL | `messages` or `friend_request` |
| People who commented on a video | `comments_video` | Facebook video URL | `messages` |
| The user's friends | `friends` | The user's profile URL | `messages` (or `unfriend_friend` to clean up) |
| Pending friend requests the user sent | `pending_friend_requests` | none | `messages` or `cancel_friend_request` |
| Facebook's friend suggestions | `suggestions` | none | `friend_request` |
| A list of profiles (CSV, sheet, scrape) | `file_upload` | none; add contacts after create | `messages` |

## Create a campaign

1. `get_user` → `team_id`.
2. `list_app_states` → the Facebook account ids → `executor_ids` (and `executor_id` = first id).
3. `create_campaign { body }`, created paused:

```json
{
  "body": {
    "name": "Post commenters DM",
    "team_id": 7715,
    "platform": "facebook",
    "audience_type": "comments",
    "action_type": "messages",
    "post_url": "https://www.facebook.com/groups/123/posts/456/",
    "executor_id": 3807,
    "executor_ids": [3807],
    "limit": 30,
    "status": 0,
    "messages": [
      { "text": ["Hi {{first_name}}, saw your comment on the post — happy to help.", "Hey {{first_name}}! Thanks for commenting."], "delay": 0 },
      { "text": ["Just checking in, {{first_name}} — any questions?"], "delay": 1440 }
    ]
  }
}
```

4. Show the user a summary (audience, messages, daily limit). On approval: `update_campaign { id, body: { status: 1 } }`.

**Messages**
- `messages[]` are sequence steps; `delay` is minutes after the previous step. Each `text[]` holds spin variants (one is picked per person).
- Placeholders: `{{name}}`, `{{first_name}}`, plus any custom key you put in contact `variables` (for example `{{company}}`).

**Friend request + DM:** `action_type: "friend_request"` plus `friend_msg_sequence`: `0` = friend request only, `1` = send the `messages` sequence when the request is sent, `2` = send it once they accept. Add `new_conversation: 1` to skip people the user already has a conversation with.

**Useful filters:** `limit` (daily cap, default 30), `skip_admin` (skip group admins), `skip_duplicate` (skip people already in another active campaign), `audience_keywords` / `exclude_keywords` (bio filters), `comment_keywords` (only commenters who wrote these), `mutual_friend_count`, `gender`, `countries`, `schedule: [{ day, start_time, end_time }]`, `clone_from` (copy an existing campaign).

## Add a lead list to an existing campaign

```json
{
  "campaign_id": 18312,
  "audience": [
    { "name": "Sara Ahmed", "profile_url": "https://www.facebook.com/sara.example", "company": "Agency PK" }
  ]
}
```

- Each row needs `profile_url` and/or `member_id` (Facebook id); rows with neither are skipped.
- Extra fields (like `company`) become message variables (`{{company}}`).
- Send up to 500 rows per call. Verify with `list_contacts { campaign_id }`.

## Errors

- `422` on `group`, `comments`, or `reactions`: the group isn't synced in the user's account. Check `list_groups`, or ask the user to sync groups from their connected Facebook browser.

## Good practice

- Start at a modest daily `limit` (20–30), especially on new or recently connected accounts.
- Refer to what the person did ("saw your comment about…") and give 2–3 variants per step.
