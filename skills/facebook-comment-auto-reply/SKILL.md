---
name: facebook-comment-auto-reply
description: "Facebook comment auto reply and comment-to-DM with ReachOwl: when someone comments a keyword (like 'price' or 'info') on a Facebook post or group post, reply publicly and send them a private Messenger DM. Use for Facebook comment-to-DM funnels, lead magnets, giveaway and listing posts, or to pause, edit, or check activity on a Facebook comment automation."
license: MIT
compatibility: Requires the ReachOwl MCP server (https://reachowl.com/mcp) and a Facebook account connected in ReachOwl.
metadata:
  author: ReachOwl
  version: "1.2.0"
  homepage: https://reachowl.com
  tags: facebook-comment-auto-reply, comment-to-dm, facebook-automation, facebook-marketing, auto-reply, reachowl
---

# Facebook Comment Auto Reply & Comment-to-DM (ReachOwl)

Watch Facebook posts for comments with a keyword, reply under the comment, and DM the commenter, all from the user's connected Facebook account.

For Instagram posts, use `instagram-comment-auto-reply`.

## Before you start

- Requires the ReachOwl MCP server connected with the user's token. If a tool reports a missing token, run the `reachowl-auth` flow.
- Show the user the reply and DM text before creating an `active` automation.

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_comment_automations` | `{ type: "facebook" }` |
| `get_comment_automation` | `{ id }` |
| `create_comment_automation` | `{ body }` |
| `update_comment_automation` | `{ id, body }` |
| `delete_comment_automation` | `{ id }` |
| `list_comment_automation_executions` | `{ id, status?, page?, per_page? }` — reply/DM log |
| `list_app_states` | Connected accounts → a `facebook` one for `executor_id` |

## Create

| Field | Notes |
|-------|-------|
| `name` | Required |
| `type` | `"facebook"` |
| `executor_id` | Required — the Facebook account that owns or can comment on the post |
| `keywords` | Required — any match triggers, e.g. `["price", "info", "interested"]` |
| `posts` | Required — `[{ "post_url": "https://www.facebook.com/…" }]`; page posts and group posts both work |
| `public_reply_message` | Required — string or array of variants |
| `private_dm_message` | Required — string or array of variants (sent in Messenger) |
| `status` | `active` or `paused` |

Placeholders: `{{name}}`, `{{first_name}}`, `{{last_name}}`.

```json
{
  "body": {
    "name": "Price replies – DHA listing",
    "type": "facebook",
    "executor_id": 3807,
    "status": "active",
    "keywords": ["price", "rate", "how much"],
    "posts": [{ "post_url": "https://www.facebook.com/groups/123/posts/456" }],
    "public_reply_message": ["Thanks {{first_name}}! Sent you the details in DM.", "Check your inbox, {{first_name}} 👋"],
    "private_dm_message": ["Hi {{first_name}}, the asking price is 2.4 crore. Want to book a visit this week?"]
  }
}
```

## Manage

- Pause / resume: `update_comment_automation { id, body: { status: "paused" } }` / `"active"`.
- Watch different posts: `update_comment_automation { id, body: { posts: [{ post_url }] } }` (replaces the list).
- Check results: `list_comment_automation_executions { id, status: "failed" }` — statuses `pending`, `processing`, `completed`, `skipped`, `failed`.
- To DM everyone who already commented (not only new keyword comments), use `facebook-dm-automation` with `audience_type: "comments"`.

## Good practice

- Ask for the keyword in the post itself ("Comment PRICE and I'll DM you").
- Provide 2–3 variants for both the reply and the DM so they don't look automated.
