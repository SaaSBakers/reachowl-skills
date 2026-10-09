---
name: reachowl-queues
description: >-
  Look up and retry ReachOwl message queues for Cold DM / follow-up delivery.
  Use when messages failed, stuck, need retry, or the user asks about queue status
  for a contact/campaign. Requires ReachOwl auth first.
---

# ReachOwl Queues & Follow-ups

Inspect and retry outbound message queues. Auth first (`reachowl-auth`).

Also read: `../COMMON.md`

## MCP tools

| Tool | Purpose |
|------|---------|
| `lookup_queues` | Find queues for confirmation |
| `update_queue` | Update/retry a queue item |

## lookup_queues requirements

Require **`campaign_id`** plus **one of**:

- `audience_id`
- `member_id`
- `profile_url`

## Retry workflow

1. `lookup_queues` with campaign + identity.
2. Confirm the queue row with the user if ambiguous.
3. Prefer `update_queue` with `{ id, retry: true }`.

## Do not

- Retry forever on permanent auth/validation failures.
- Change campaign messages here — use campaigns / templates skills.

## Example

```
User: Retry WhatsApp message for +923001234567 on campaign 18312
→ lookup_queues { campaign_id: 18312, member_id: "923001234567@c.us" }
→ update_queue { id: <queue_id>, retry: true }
```
