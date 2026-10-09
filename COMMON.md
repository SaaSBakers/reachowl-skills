# ReachOwl shared rules (all skills)

## MCP endpoint

- Remote MCP: `https://reachowl.com/mcp`
- REST origin (same APIs MCP wraps): `https://reachowl.com` → `/api/v1/*`
- Do **not** call `/mcp` as a REST base URL from scripts/Actors

## Auth (required before other tools)

1. Prefer Sanctum token shape `digits|…` (not Claude/Smithery OAuth).
2. Store via connector `reachowlToken` / `x-reachowl-token`, or MCP `set_token`.
3. `auth_status` → expect `has_token: true`.
4. `get_user` → validates token; use returned `team_id` / teams when creating resources.
5. Fallback only: `authenticate` with email/password if user has no token.
6. Never print full tokens in chat logs.

## Hard rules

- **Never** use `create_campaign` to upload contacts into an existing campaign → use `add_campaign_contacts`.
- Prefer Facebook **group URLs** / **post URLs** over internal IDs.
- Browsers / app-states are **read-only**.
- Prefer omit `team_id` on list calls; resolve from `get_user` when creating.
- Toggle campaigns with `{ "status": 0|1 }`; comment automations with `{ "status": "active"|"paused" }`.
- Lead finding (Apify) ≠ outreach (MCP). Actor finds/imports; MCP configures Cold DM / follow-ups / queues.

## Platforms

`facebook` | `instagram` | `whatsapp`
