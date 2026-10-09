---
name: reachowl-team-accounts
description: >-
  Manage ReachOwl teams/members and inspect connected browsers and social accounts
  (app-states). Use when inviting teammates, listing teams, or choosing executor
  accounts for campaigns. Requires ReachOwl auth first.
---

# ReachOwl Team & Connected Accounts

Team membership + read-only browsers/app-states. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

### Team

| Tool | Purpose |
|------|---------|
| `list_teams` | Teams + `team_id` |
| `list_team_members` | Members |
| `add_team_member` | Invite by email |
| `update_team_member` | Update `{ id, user_id, status, role }` |
| `delete_team_member` | Remove member |

### Accounts (read-only)

| Tool | Purpose |
|------|---------|
| `list_browsers` | Connected browsers |
| `get_browser` | Browser detail |
| `list_app_states` | Connected social accounts → use as `executor_id` / `executor_ids` |
| `get_app_state` | App-state detail |

## Rules

- Browsers and app-states are **read-only** via MCP (no connect/disconnect here).
- Always resolve executors with `list_app_states` before `create_campaign` / comment automations.
- Prefer omit `team_id` on list calls unless filtering is required.

## Example

```
User: Which Instagram accounts can I use for a campaign?
→ list_app_states
→ summarize usernames/platforms/ids as executor options
```
