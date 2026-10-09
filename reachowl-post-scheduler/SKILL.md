---
name: reachowl-post-scheduler
description: >-
  Schedule and manage ReachOwl Facebook group post schedulers.
  Use when the user wants to schedule posts to Facebook groups, list schedulers,
  update schedules, or manage post queues. Requires ReachOwl auth first.
---

# ReachOwl Post Scheduler

Schedule posts to Facebook groups. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_groups` | Synced Facebook groups (read-only) |
| `list_post_schedulers` | List schedulers |
| `get_post_scheduler` | Scheduler detail |
| `create_post_scheduler` | Create; prefer `group_urls`; supports `clone_from` |
| `update_post_scheduler` | Status, schedule, `delete_queue_ids`, or posts update |
| `delete_post_scheduler` | Delete scheduler |

## Rules

- Prefer **group URLs** over internal group IDs.
- Use `list_groups` to discover available groups when URLs unknown.
- Prefer `clone_from` when duplicating an existing scheduler.

## Example

```
User: Schedule this post to my DHA Facebook groups tomorrow
→ list_groups
→ create_post_scheduler { group_urls: […], posts: […], schedule: … }
```
