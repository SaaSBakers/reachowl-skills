---
name: reachowl-api
description: >-
  Escape hatch for authenticated raw ReachOwl V1 API calls when no named MCP tool
  fits. Use only after preferring named ReachOwl MCP tools. Requires ReachOwl auth first.
---

# ReachOwl API Escape Hatch

Raw `/api/v1/*` access when named tools are insufficient. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tool

| Tool | Purpose |
|------|---------|
| `api_request` | Authenticated call; path **must** start with `/api/v1/` |

## Rules

1. Prefer named MCP tools from other ReachOwl skills whenever possible.
2. Path must begin with `/api/v1/`.
3. Do not invent undocumented endpoints — check `list_events` / existing tools first.
4. Never log Authorization headers or tokens.
5. Still obey: no `create_campaign` for contact uploads; use `add_campaign_contacts`.

## Example

```
User: Call a V1 endpoint that has no dedicated tool yet
→ confirm path is /api/v1/…
→ api_request { method, path, body? }
```
