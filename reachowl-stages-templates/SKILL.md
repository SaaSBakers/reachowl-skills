---
name: reachowl-stages-templates
description: >-
  Manage ReachOwl pipeline stages and message templates used in Cold DM sequences.
  Use when the user mentions stages, pipeline, templates, follow-up copy, or
  message templates. Requires ReachOwl auth first.
---

# ReachOwl Stages & Message Templates

Configure CRM stages and reusable message templates. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

### Stages

| Tool | Purpose |
|------|---------|
| `list_stages` | List stages |
| `get_stage` | Stage detail |
| `create_or_update_stage` | Create or update `{ name, id?, order? }` |
| `reorder_stages` | Reorder via `[{ id, order }]` |
| `delete_stage` | Delete stage |

### Templates

| Tool | Purpose |
|------|---------|
| `list_message_templates` | List templates |
| `upsert_message_template` | Create/update `{ message, stage_id, image?, id? }` |
| `delete_message_template` | Delete template |

## Workflow

1. List stages before assigning template `stage_id`.
2. Upsert templates with clear message body; attach `stage_id` when relevant.
3. Reorder stages with explicit `{ id, order }` pairs.

## Example

```
User: Add a "Interested" stage and a follow-up template
→ create_or_update_stage { name: "Interested" }
→ upsert_message_template { stage_id, message: "Thanks for your interest…" }
```
