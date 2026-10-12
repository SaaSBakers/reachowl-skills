---
name: instagram-comment-auto-reply
description: "Instagram comment auto reply and comment-to-DM with ReachOwl: when someone comments a keyword (like 'link', 'info', or 'price') on an Instagram post, reply publicly and send them a private Instagram DM. Use for Instagram comment-to-DM funnels, 'comment LINK to get it' lead magnets, giveaways, or to pause, edit, or check activity on an Instagram comment automation."
license: MIT
compatibility: Requires the ReachOwl MCP server (https://reachowl.com/mcp) and an Instagram account connected in ReachOwl.
metadata:
  author: ReachOwl
  version: "1.2.0"
  homepage: https://reachowl.com
  tags: instagram-comment-auto-reply, comment-to-dm, instagram-automation, instagram-marketing, auto-reply, reachowl
---

# Instagram Comment Auto Reply & Comment-to-DM (ReachOwl)

Watch Instagram posts for comments with a keyword, reply under the comment, and DM the commenter, all from the user's connected Instagram account. This is the "comment LINK and I'll send it" funnel.

For Facebook posts, use `facebook-comment-auto-reply`.

## Before you start

- Requires the ReachOwl MCP server connected with the user's token. If a tool reports a missing token, run the `reachowl-auth` flow.
- Show the user the reply and DM text before creating an `active` automation.

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_comment_automations` | `{ type: "instagram" }` |
| `get_comment_automation` | `{ id }` |
| `create_comment_automation` | `{ body }` |
| `update_comment_automation` | `{ id, body }` |
| `delete_comment_automation` | `{ id }` |
| `list_comment_automation_executions` | `{ id, status?, page?, per_page? }` — reply/DM log |
| `list_app_states` | Connected accounts → an `instagram` one for `executor_id` |

## Create

| Field | Notes |
|-------|-------|
| `name` | Required |
| `type` | `"instagram"` |
| `executor_id` | Required — the Instagram account that owns the post |
| `keywords` | Required — any match triggers, e.g. `["link", "guide", "info"]` |
| `posts` | Required — `[{ "post_url": "https://www.instagram.com/p/…" }]` |
| `public_reply_message` | Required — string or array of variants |
| `private_dm_message` | Required — string or array of variants |
| `status` | `active` or `paused` |

Placeholders: `{{name}}`, `{{first_name}}`, `{{last_name}}`.

```json
{
  "body": {
    "name": "Free guide – comment LINK",
    "type": "instagram",
    "executor_id": 55,
    "status": "active",
    "keywords": ["link", "guide"],
    "posts": [{ "post_url": "https://www.instagram.com/p/ABC123/" }],
    "public_reply_message": ["Sent! Check your DMs {{first_name}} 📩", "Just DM'd you the link, {{first_name}}!"],
    "private_dm_message": ["Hey {{first_name}}! Here's the free guide: https://example.com/guide — tell me what you think!"]
  }
}
```

## Manage

- Pause / resume: `update_comment_automation { id, body: { status: "paused" } }` / `"active"`.
- Watch different posts: `update_comment_automation { id, body: { posts: [{ post_url }] } }` (replaces the list).
- Check results: `list_comment_automation_executions { id, status: "failed" }` — statuses `pending`, `processing`, `completed`, `skipped`, `failed`.
- To DM everyone who already commented on a post, use `instagram-dm-automation` with `audience_type: "comments"`.

## Good practice

- Put the keyword in the caption and on-screen ("Comment LINK"), and keep it one word.
- Provide 2–3 variants for both the reply and the DM so they don't look automated.
