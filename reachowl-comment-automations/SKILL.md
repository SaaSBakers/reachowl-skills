---
name: reachowl-comment-automations
description: >-
  Create and manage ReachOwl Facebook/Instagram comment reply automations
  (public reply + private DM). Use when the user mentions auto-reply to comments,
  comment keywords, comment DM automations, or executions. Requires ReachOwl auth first.
---

# ReachOwl Comment Automations

Auto public reply + private DM on FB/IG comments. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_comment_automations` | List automations |
| `get_comment_automation` | Detail |
| `create_comment_automation` | Create automation |
| `update_comment_automation` | Partial update; pause/resume |
| `delete_comment_automation` | Delete |
| `list_comment_automation_executions` | Reply/DM activity log |

## Create requirements

Required fields:

- `name`
- `executor_id` (from `list_app_states`)
- `keywords[]`
- `posts[{ post_url }]` — prefer post URLs
- `public_reply_message`
- `private_dm_message`

Optional: `type` (`facebook` | `instagram`)

## Pause / resume

```
update_comment_automation { id, status: "paused" }
update_comment_automation { id, status: "active" }
```

Replace monitored posts with `posts: [{ post_url }]`.

## Example

```
User: Auto-reply on this FB post when people comment "price"
→ list_app_states
→ create_comment_automation {
    name: "Price replies",
    executor_id,
    keywords: ["price"],
    posts: [{ post_url: "https://facebook.com/…" }],
    public_reply_message: "Thanks! DMing you…",
    private_dm_message: "Here are details…"
  }
```
