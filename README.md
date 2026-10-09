# ReachOwl Agent Skills

Agent skills and an MCP server that let Claude, Cursor, Codex, and other AI agents run **Facebook, Instagram, and WhatsApp marketing** with [ReachOwl](https://reachowl.com): social listening, signal-based outreach, cold DM and WhatsApp campaigns, Facebook group posting, comment-to-DM automations, Instagram growth, and lead finding.

```
"Find people in my Facebook groups asking for a realtor in Lahore and DM them."
"Turn this blog post into posts for my landlord groups and schedule them every 30 minutes."
"Send a WhatsApp follow-up to everyone in this CSV, personalized by area."
```

Every action runs from the user's own connected accounts in ReachOwl. The agent does the planning, writing, and setup, and asks before anything is sent or posted.

## What you can do

| Use case | What the agent does | Skill |
|----------|---------------------|-------|
| **Social listening** | Monitors keywords in Facebook groups, Instagram, Reddit, and Quora, and summarizes what people are asking for | `reachowl-mentions` |
| **Signal-based marketing** | Finds people who just posted a need, qualifies them, and DMs them with a message about what they asked | `reachowl-signal-outreach` |
| **Content distribution** | Repurposes an article or announcement into posts for each community, schedules them, and captures interested commenters | `reachowl-content-distribution` |
| **Community posting** | Schedules posts to many Facebook groups on an interval or calendar | `reachowl-post-scheduler` |
| **WhatsApp marketing** | Imports a phone list or group, writes personalized sequences, launches, tracks replies, and retries failures | `reachowl-whatsapp-marketing` |
| **Cold DM campaigns** | DMs or friend-requests group members, post commenters and reactors, followers, or hashtag audiences | `reachowl-campaigns` |
| **Comment-to-DM** | Replies publicly and DMs anyone who comments a keyword on a Facebook or Instagram post | `reachowl-comment-automations` |
| **Instagram growth** | Auto-likes and reposts niche posts by hashtag, search, or Explore | `reachowl-profile-growth` |
| **Lead finding** | Scrapes and scores leads from websites with the [ReachOwl Lead Finder](https://apify.com/superior_couch/reachowl-lead-finder) Apify Actor and imports them into a campaign | `reachowl-lead-finder` |

## All skills

| Skill | Purpose |
|-------|---------|
| [`reachowl`](skills/reachowl/SKILL.md) | Start here — routes a request to the right skill |
| [`reachowl-auth`](skills/reachowl-auth/SKILL.md) | Connect and verify a ReachOwl account |
| [`reachowl-signal-outreach`](skills/reachowl-signal-outreach/SKILL.md) | Intent signals → qualified leads → personalized outreach |
| [`reachowl-whatsapp-marketing`](skills/reachowl-whatsapp-marketing/SKILL.md) | End-to-end WhatsApp campaigns |
| [`reachowl-content-distribution`](skills/reachowl-content-distribution/SKILL.md) | Repurpose content and distribute it to communities |
| [`reachowl-mentions`](skills/reachowl-mentions/SKILL.md) | Keyword monitors and found posts |
| [`reachowl-campaigns`](skills/reachowl-campaigns/SKILL.md) | Facebook, Instagram, and WhatsApp outreach campaigns and contact imports |
| [`reachowl-post-scheduler`](skills/reachowl-post-scheduler/SKILL.md) | Facebook group post scheduling |
| [`reachowl-comment-automations`](skills/reachowl-comment-automations/SKILL.md) | Comment auto-reply and DM |
| [`reachowl-profile-growth`](skills/reachowl-profile-growth/SKILL.md) | Instagram growth plans |
| [`reachowl-lead-finder`](skills/reachowl-lead-finder/SKILL.md) | Website lead scraping with Apify |
| [`reachowl-contacts`](skills/reachowl-contacts/SKILL.md) | Leads and CRM notes |
| [`reachowl-queues`](skills/reachowl-queues/SKILL.md) | Retry failed or stuck messages |
| [`reachowl-stages-templates`](skills/reachowl-stages-templates/SKILL.md) | Pipeline stages and message templates |
| [`reachowl-team-accounts`](skills/reachowl-team-accounts/SKILL.md) | Connected accounts and team members |
| [`reachowl-webhooks`](skills/reachowl-webhooks/SKILL.md) | Send events to Slack, Zapier, n8n, or a CRM |
| [`reachowl-api`](skills/reachowl-api/SKILL.md) | Raw V1 API calls when no named tool fits |

## Setup

You need:

1. A [ReachOwl](https://reachowl.com) account with at least one Facebook, Instagram, or WhatsApp account connected through the ReachOwl extension.
2. Your **ReachOwl API token** (looks like `123|abc…`). Find it in the ReachOwl web app under **ReachOwl MCP Tool**, or create one:

   ```bash
   curl -X POST https://reachowl.com/api/v1/authenticate \
     -H "Content-Type: application/json" \
     -d '{"email":"you@example.com","password":"…"}'
   ```

3. The ReachOwl MCP server: **`https://reachowl.com/mcp`** (Streamable HTTP). Send your token in the `x-reachowl-token` header, or as `Authorization: Bearer 123|…`. If your client can't set headers, connect without one and give the agent your token in chat (it calls `set_token`).

### Claude Code (plugin — skills and MCP in one step)

```
/plugin marketplace add SaaSBakers/reachowl-skills
/plugin install reachowl@reachowl
```

Claude Code asks for your ReachOwl API token when the plugin is enabled and connects the MCP server for you.

### Any agent (skills CLI)

Install the skills with [`skills`](https://skills.sh) (works with Claude Code, Cursor, Codex, and many other agents):

```bash
npx skills add SaaSBakers/reachowl-skills
```

Then add the MCP server to your agent as shown below.

### Claude Code (MCP only)

```bash
claude mcp add --transport http reachowl https://reachowl.com/mcp \
  --header "x-reachowl-token: 123|your-token"
```

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "reachowl": {
      "url": "https://reachowl.com/mcp",
      "headers": { "x-reachowl-token": "123|your-token" }
    }
  }
}
```

Skills: run `npx skills add SaaSBakers/reachowl-skills`, or copy folders from `skills/` into `~/.cursor/skills/`.

### Claude.ai and Claude Desktop

1. **Settings → Connectors → Add custom connector** → URL `https://reachowl.com/mcp`.
2. Upload skills under **Settings → Capabilities → Skills**. Zip a folder from `skills/`, for example `reachowl-campaigns/`, and upload it.
3. In a chat, say "Connect my ReachOwl account" and paste your token.

### ChatGPT and other remote MCP clients

Add `https://reachowl.com/mcp` as a remote MCP server and use your token as the API key (`Authorization: Bearer 123|…`).

### Check it works

Ask your agent: **"Check my ReachOwl connection and list my connected accounts."** It should call `auth_status`, `get_user`, and `list_app_states`.

## How it works

```
You ──► AI agent + ReachOwl skills ──► ReachOwl MCP (https://reachowl.com/mcp) ──► ReachOwl V1 API
                                                                                     │
                                         your connected Facebook / Instagram / WhatsApp accounts
```

- **Skills** teach the agent the workflows, the right tools and fields, and the rules.
- **The MCP server** exposes 63 tools over the ReachOwl V1 API (rate limit: 120 requests per minute).
- **The ReachOwl extension** carries out actions through your connected accounts, at the daily limits you set.

## Safety

- The skills tell the agent to create campaigns, schedulers, and automations **paused**, show you the audience and messages, and switch them on only after you approve.
- Your token stays in your client's MCP config or on the MCP session. Skills never print it.
- Use the skills only for contacts and communities where outreach is welcome, and follow each platform's terms and each group's rules.

## Links

- ReachOwl: <https://reachowl.com>
- MCP server: `https://reachowl.com/mcp`
- Lead Finder Actor: <https://apify.com/superior_couch/reachowl-lead-finder>
- Skill format: [Agent Skills](https://agentskills.io)

## License

[MIT](LICENSE)
