---
name: reachowl-auth
description: >-
  Authenticate and verify ReachOwl MCP access with Sanctum tokens.
  Use when connecting ReachOwl, setting a token, checking auth_status,
  calling get_user, logging in with email/password, or clearing a session token.
---

# ReachOwl Auth

Connect a user to ReachOwl MCP before any other ReachOwl skill.

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `set_token` | Store Sanctum token `id\|…` on this session |
| `auth_status` | Check `has_token` (does not call API) |
| `get_user` | Validate token; return user + teams |
| `authenticate` | Fallback: mint token via email/password |
| `clear_token` | Remove token from session |

## Workflow

1. If user pastes a token → `set_token` with that value.
2. If connector already provided `reachowlToken` → skip set; go to step 3.
3. Call `auth_status`. If `has_token` is false, ask for Sanctum token (or email/password).
4. Call `get_user`. On success, confirm identity and default `team_id`.
5. Only if user has no token: `authenticate` with email/password, then `get_user` again.
6. On logout / switch account: `clear_token`.

## Do not

- Treat Claude/Smithery “connected” as a ReachOwl login.
- Accept OAuth-looking tokens that are not `digits|…`.
- Echo the full token back to the user.

## Example

```
User: Connect my ReachOwl account. Token is 12|abc…
→ set_token { token: "12|abc…" }
→ auth_status
→ get_user
→ "Connected as … (team_id …)"
```
