---
name: reachowl-campaigns
description: >-
  Create, list, update, start/pause ReachOwl outreach campaigns and add contacts
  to existing campaigns (Cold DM / messaging). Use when the user mentions campaigns,
  Start Campaign, Cold DM, WhatsApp/Facebook/Instagram outreach, or uploading leads
  into a campaign. Requires ReachOwl auth first.
---

# ReachOwl Campaigns (Cold DM)

Manage ReachOwl campaigns via MCP. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `list_campaigns` | List campaigns; filter `platform` |
| `get_campaign` | Campaign detail |
| `create_campaign` | Create a **new** campaign only |
| `update_campaign` | Partial update; `{ status: 0\|1 }` to pause/start |
| `delete_campaign` | Delete campaign |
| `add_campaign_contacts` | Upload contacts to an **existing** campaign |

## Critical

**Never** call `create_campaign` just to upload contacts.  
If user says “add these leads to campaign 18312” → `add_campaign_contacts`.

## Create campaign workflow (Start Campaign wizard)

1. `get_user` → `team_id`
2. `list_app_states` → pick `executor_ids` / `executor_id` (connected accounts)
3. `create_campaign` with wizard fields:

| Step | Fields |
|------|--------|
| Audience | `platform`, `audience_type` |
| Source | `post_url` (unless pending/suggestions/file_upload) |
| Purpose | `action_type` (`messages`, `comments`, …) |
| Info | `name`, `executor_ids` (+ `executor_id`) |
| Messages | `messages` when `action_type=messages` |

**Runnable minimum:** `name`, `platform`, `audience_type`, `action_type`, `executor_ids`, `post_url` (when required), `messages` when messaging.

### WhatsApp

- `platform: "whatsapp"`, `action_type: "messages"`
- `audience_type: "group"` or `"file_upload"`
- WhatsApp must be activated in Browsers first

## Add contacts to existing campaign

```
add_campaign_contacts {
  campaign_id: 18312,
  audience: [
    {
      name: "Ali Khan",
      member_id: "923001234567@c.us",
      profile_url: "https://wa.me/923001234567",
      variables: { area: "DHA Lahore" }
    }
  ]
}
```

- WhatsApp rows need phone → `member_id` like `92300…@c.us` + `wa.me` URL (or pass `phone` and let the tool map).
- FB/IG rows need `member_id` and/or `profile_url`.
- Aliases: `contacts` / `audiences` → `audience`.

## Start / pause

```
update_campaign { id, status: 1 }  // start
update_campaign { id, status: 0 }  // pause
```

## Examples

**New campaign**

```
User: Start a WhatsApp campaign for my DHA leads file audience
→ get_user, list_app_states
→ create_campaign { platform: "whatsapp", action_type: "messages", audience_type: "file_upload", … }
```

**Upload to existing**

```
User: Upload these numbers to campaign 18312
→ add_campaign_contacts { campaign_id: 18312, audience: […] }
→ get_campaign / list_contacts to verify
```
