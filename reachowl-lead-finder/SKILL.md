---
name: reachowl-lead-finder
description: >-
  Find and qualify leads from public website URLs using the ReachOwl Lead Finder
  Apify Actor, then optionally import into an existing ReachOwl campaign and
  continue outreach via MCP. Use when the user asks to scrape/find leads from
  websites, directories, or URLs and add them to ReachOwl. Do not implement Cold DM
  inside the Actor — use ReachOwl MCP after import.
---

# ReachOwl Lead Finder

Apify Actor finds leads; ReachOwl MCP owns campaigns / Cold DM / follow-ups.

Also read: `../COMMON.md`

## Separation of duties

```
User request
  → Apify Actor "ReachOwl Lead Finder"
      (crawl → extract → score → dedupe → optional import)
  → ReachOwl MCP
      (verify campaign/contacts → configure outreach → Cold DM / queues)
```

| Layer | May do | Must not do |
|-------|--------|-------------|
| Apify Actor | Public crawl, qualify, dedupe, import via V1 contacts API | Create campaigns, Cold DM, follow-ups, AI message decisions |
| ReachOwl MCP | Campaigns, contacts, queues, templates, stages | Duplicate a separate scrape pipeline |

## Prerequisites

1. User is authenticated to ReachOwl MCP (`reachowl-auth`).
2. An **existing** campaign id if importing (`list_campaigns` / `get_campaign`).
3. Apify Actor secrets: `REACHOWL_API_URL=https://reachowl.com`, `REACHOWL_API_KEY` (Sanctum token).
4. WhatsApp campaigns need phone numbers on leads to import.

## Actor input

```json
{
  "startUrls": [{ "url": "https://example.com/agents" }],
  "targetAudience": "real estate agents",
  "location": "Lahore, Pakistan",
  "keywords": ["real estate", "property dealer", "real estate agent"],
  "maxLeads": 100,
  "sendToReachOwl": true,
  "reachOwlCampaignId": "EXISTING_CAMPAIGN_ID"
}
```

- Default `sendToReachOwl: false` (scrape-only).
- Never auto-create a campaign from the Actor.
- `reachOwlAudienceId` is optional/forward-compat only; import targets the campaign.

## Agent workflow

1. Confirm URLs + audience + location + max leads.
2. If importing: pick existing campaign via MCP (`list_campaigns`). **Do not** `create_campaign` just to upload.
3. Start Apify Actor **ReachOwl Lead Finder** with input above.
4. Read Dataset + Key-Value `STATS` / `OUTPUT`.
5. Via MCP: `get_campaign` + `list_contacts { campaign_id }` to verify.
6. Only then configure messages / start campaign / queues (`reachowl-campaigns`, `reachowl-queues`).

## Import API (same as MCP)

Actor uses: `POST /api/v1/campaigns/{id}/contacts` `{ audience: [...] }`  
Same contract as MCP `add_campaign_contacts`.

## Example user request

> Find 100 real estate agents in Lahore from these websites and add them to my existing ReachOwl campaign.

```
→ auth + list_campaigns / get_campaign
→ run Apify Actor with sendToReachOwl=true + campaign id
→ list_contacts to verify
→ update_campaign / templates / queues for outreach
```
