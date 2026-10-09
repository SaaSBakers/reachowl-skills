---
name: reachowl-contacts
description: >-
  List and inspect ReachOwl campaign contacts/audiences and manage contact notes.
  Use when the user asks about leads in a campaign, contact details, CRM notes,
  or verifying imports. Requires ReachOwl auth first.
---

# ReachOwl Contacts & CRM notes

Work with audience/contact rows under campaigns. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_contacts` | List contacts; pass `campaign_id` to filter |
| `get_contact` | Contact detail including notes |
| `add_contact_note` | Add/update a note on a contact |
| `delete_note` | Delete a note by note id |

## Workflow

1. Prefer `list_contacts` with `campaign_id` when scoped to one campaign.
2. Use `get_contact` for full detail + notes.
3. Add notes with `add_contact_note` (contact id + note body).
4. To **create** contacts, use `add_campaign_contacts` (campaigns skill) — not these list tools.

## Do not

- Invent contact fields that were not returned by the API.
- Create campaigns here; that belongs to `reachowl-campaigns`.

## Example

```
User: Show contacts in campaign 18312 and note that Ali replied yes
→ list_contacts { campaign_id: 18312 }
→ get_contact { id }
→ add_contact_note { id, note: "Replied yes" }
```
