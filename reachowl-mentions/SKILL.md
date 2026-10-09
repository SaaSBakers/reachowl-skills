---
name: reachowl-mentions
description: >-
  Manage ReachOwl keyword monitors (social listening) and found posts.
  Use when the user mentions keyword monitoring, mentions, social listening,
  brand monitoring, or posts found by keywords. Requires ReachOwl auth first.
---

# ReachOwl Keyword Mentions (Social Listening)

Create and review keyword monitors. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_mentions` | List keyword monitors |
| `get_mention` | Monitor detail |
| `create_or_update_mention` | Create (no id) or update (with id) |
| `delete_mention` | Delete monitor |
| `list_mention_posts` | Posts found for a monitor (`mention_id`) |
| `delete_mention_post` | Delete a found post |

## Workflow

1. `list_mentions` to see existing monitors.
2. Create/update with `create_or_update_mention`.
3. Review hits with `list_mention_posts { mention_id }`.
4. For webhook alerts of new posts, use `reachowl-webhooks` (`keyword-monitor-found-a-post`).

## Webhook note

`keyword-monitor-found-a-post` sends at most **2 new posts** per store event and ignores webhook `campaign_id`.

## Example

```
User: Monitor "real estate Lahore" and show me found posts
→ create_or_update_mention { …keywords… }
→ list_mention_posts { mention_id }
```
