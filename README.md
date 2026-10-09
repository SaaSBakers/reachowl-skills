# ReachOwl Agent Skills

Claude / Cursor Agent Skills for ReachOwl MCP (`https://reachowl.com/mcp`).

Each folder is a publishable skill with a `SKILL.md`. Shared rules: [`COMMON.md`](./COMMON.md).

## Skills

| Skill folder | Publish name | Use for |
|--------------|--------------|---------|
| `reachowl-auth` | ReachOwl Auth | Token connect / verify |
| `reachowl-campaigns` | ReachOwl Campaigns | Cold DM campaigns + add contacts |
| `reachowl-contacts` | ReachOwl Contacts | List contacts + notes |
| `reachowl-queues` | ReachOwl Queues | Lookup / retry message queues |
| `reachowl-stages-templates` | ReachOwl Stages & Templates | Pipeline + message templates |
| `reachowl-mentions` | ReachOwl Mentions | Keyword social listening |
| `reachowl-post-scheduler` | ReachOwl Post Scheduler | FB group post scheduling |
| `reachowl-profile-growth` | ReachOwl Profile Growth | Instagram growth plans |
| `reachowl-comment-automations` | ReachOwl Comment Automations | Comment reply + DM |
| `reachowl-webhooks` | ReachOwl Webhooks | Event callbacks |
| `reachowl-team-accounts` | ReachOwl Team & Accounts | Teams + browsers/app-states |
| `reachowl-lead-finder` | ReachOwl Lead Finder | Apify scrape → existing campaign |
| `reachowl-api` | ReachOwl API | Raw `/api/v1/*` escape hatch |

## Suggested publish order (Claude directory)

1. `reachowl-auth`
2. `reachowl-campaigns`
3. `reachowl-lead-finder`
4. `reachowl-mentions` + `reachowl-comment-automations`
5. Remaining skills as needed

## MCP prerequisite

Users must connect ReachOwl MCP and provide a Sanctum token (`id|…`):

- Remote: `https://reachowl.com/mcp`
- See `../MCP/README.md`

## Local install (Cursor)

Copy a skill folder into your Agent Store skills path, or project `.cursor/skills/<name>/`.

Example:

```bash
cp -R reachowl-skills/reachowl-campaigns ~/.cursor/skills/reachowl-campaigns
```

(Or your user Agent Store `skills/` path if configured.)

## Hard rules (all skills)

- Auth before other tools
- Never `create_campaign` to upload contacts → `add_campaign_contacts`
- Actor finds leads; MCP does outreach
- Prefer public URLs over internal IDs
