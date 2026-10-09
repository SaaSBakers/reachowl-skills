---
name: reachowl-webhooks
description: >-
  Subscribe ReachOwl webhook callbacks for campaign and keyword-monitor events.
  Use when the user wants notifications, webhook URLs, event subscriptions, or
  integrations with external systems. Requires ReachOwl auth first.
---

# ReachOwl Webhooks

Subscribe callback URLs to ReachOwl events. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_events` | Event catalog (ids/slugs for subscribe) |
| `list_webhooks` | Existing webhooks |
| `create_webhook` | Subscribe URL + events |
| `update_webhook` | Update subscription |
| `delete_webhook` | Delete webhook |

## Workflow

1. `list_events` to discover valid event ids/slugs.
2. `create_webhook` with callback URL + selected events.
3. Verify with `list_webhooks`.

## Keyword monitor note

`keyword-monitor-found-a-post`:

- Fires at most once per store of new mention posts
- Includes at most **2** new posts
- Ignores webhook `campaign_id`

## Example

```
User: Send new keyword-monitor posts to https://hooks.example.com/ro
→ list_events
→ create_webhook { url: "https://hooks.example.com/ro", events: ["keyword-monitor-found-a-post"] }
```
