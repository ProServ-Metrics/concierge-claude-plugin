# Concierge Claude Plugin

A Claude Code plugin for sales and pipeline work on Concierge data: account briefs, deal reviews, forecasts, expansion plays, proposals and consultant profiles. Connects to the Concierge MCP server through its sign-in gateway.

The staffing skills (staff, find candidates, check availability, evaluate a lineup) are switched off until they run on the Concierge staffing workflow.

---

## What's included

| Component | Name | Purpose |
|-----------|------|---------|
| Skill | `/concierge:pipeline` | Pipeline overview and resource conflict analysis |
| Skill | `/concierge:build-proposal` | Client-facing proposal for an opportunity |
| Skill | `/concierge:build-game-plan` | Internal delivery game plan for an opportunity |
| Skill | `/concierge:consultant-card` / `consultant-profile` | A consultant's card or full profile as an HTML artifact |
| Skill | `/concierge:account-brief` | Sales: internal client brief before a meeting or QBR |
| Skill | `/concierge:deal-review` | Sales: qualify an opportunity — staffing readiness, estimated margin, verdict |
| Skill | `/concierge:forecast` | Sales: weighted pipeline forecast vs. delivery capacity |
| Skill | `/concierge:expansion-plays` | Sales: extensions, renewals, re-engagement and cross-sell targets |
| Skill | `/concierge:sellable-capacity` | Sales: what the bench can take on, by skill and start date |
| Agent | `@concierge:pipeline-analyst` | Analyses resource allocation (spawned by `/pipeline`) |
| MCP | `Concierge by ProServe Metrics` | The Concierge MCP server, through its sign-in gateway |

---

## Prerequisites

1. **Claude Code** (Desktop app or CLI) — download from [claude.ai/download](https://claude.ai/download), v2.1.198+ recommended (background subagents run concurrently by default)
2. A **ProServe Metrics work account** with Concierge access (your Concierge roles decide what data you can see)

---

## Step 1 — Install the plugin

```
/plugin marketplace add ProServ-Metrics/concierge-claude-plugin
/plugin install concierge@concierge-demo
```

## Step 2 — Sign in

Run `/mcp`, select **Concierge by ProServe Metrics**, and choose **Authenticate**. A browser opens:

1. **Allow** the connection on the Concierge page (asked once per device).
2. Sign in with your work account.

The browser then says you can close it. Start a new conversation (or `/clear`) so the Concierge tools load from the start.

## Step 3 — Verify

Run `/help` — the `concierge` namespace lists the skills — or ask *"who's on the bench next month?"*.

---

## Trying the skills

```
/concierge:account-brief Atlantic Shipping Co
/concierge:deal-review OPP-2024-002
/concierge:forecast next quarter
/concierge:expansion-plays
/concierge:sellable-capacity next 8 weeks
/concierge:pipeline
/concierge:build-proposal OPP-2024-002
```

The sales views are internal (they include ratings, costs or individual availability) — share client-facing material
through `/concierge:build-proposal` instead.

---

## Troubleshooting

### "No tools available" or MCP connection errors

- Run `/mcp`: if **Concierge by ProServe Metrics** isn't connected, choose **Authenticate** (or **Reconnect**).
- Signed in but no tools in the conversation: start a new one (or `/clear`) — tools from a server you sign into
  mid-conversation don't always load into it.
- "Access denied" after signing in: your account needs a Concierge role — ask a Concierge admin.

### Scouts return no candidates

- The MCP data might not have employees with the required skills in the test dataset
- Try `/concierge:find-candidates` with a broad skill term to see what's available
- Check the Fabric notebooks ran and the gold layer is populated

### Plugin not loading after edits

In Claude Code:
```
/reload-plugins
```

### Authentication pop-up not appearing

- In Desktop: ensure you are on a Local session (not Remote/Cloud)
- The OAuth flow requires a browser — ensure Claude has permission to open one

## Developing the plugin

Run the MCP server locally (`dotnet run --project MCP/Concierge.Services.MCP.csproj` in moneyball-services; in
Development, `Authorization:Enabled` is false), point `.mcp.json` at `http://localhost:5000`, and load the plugin from
source with `claude --plugin-dir ./Plugins/Claude`. Skills and agents are generated from `prompts/` by `prompts/dist.sh`.

---

## File structure

```
plugins/concierge/
├── .claude-plugin/plugin.json   ← manifest (name, version)
├── skills/<name>/SKILL.md       ← /concierge:<name> (generated from prompts/ by prompts/dist.sh)
├── agents/pipeline-analyst.md   ← @concierge:pipeline-analyst (used by /pipeline)
├── hooks/hooks.json             ← PostToolUse: MCP error hint
├── settings.json                ← system prompt
└── .mcp.json                    ← the Concierge MCP server (through its sign-in gateway)
```
