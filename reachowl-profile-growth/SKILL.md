---
name: reachowl-profile-growth
description: >-
  Manage ReachOwl Instagram profile growth plans.
  Use when the user mentions Instagram growth, profile growth, follow/engage plans,
  or pausing/running IG growth. Requires ReachOwl auth first.
---

# ReachOwl Profile Growth (Instagram)

Instagram-only growth plans. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_profile_growth` | List IG growth plans |
| `get_profile_growth` | Plan detail |
| `create_profile_growth` | Create Instagram growth plan |
| `update_profile_growth` | e.g. `{ status: "running"\|"paused" }` |
| `delete_profile_growth` | Delete plan |

## Rules

- Platform is **instagram only**.
- Resolve connected IG executors via `list_app_states` before create when needed.
- Pause/resume with `update_profile_growth` status — do not recreate the plan.

## Example

```
User: Pause my Instagram growth plan
→ list_profile_growth
→ update_profile_growth { id, status: "paused" }
```
