# Concierge Claude Plugin

A Claude Code plugin for sales and pipeline work on Concierge data: account briefs, deal reviews, forecasts, expansion plays, proposals and consultant profiles. Connects to the Concierge MCP server through its sign-in gateway.

The staffing skills (staff, find candidates, check availability, evaluate a lineup) are switched off until they run on the Concierge staffing workflow.

---

## What's included

| Component | Name | Purpose |
|-----------|------|---------|
| Skill | `/concierge:experience-match` | "Have we done this before?" — delivered work matching a scope, with evidence and an RFP-ready paragraph |
| Skill | `/concierge:expert-finder` | Who should join this call — evidence-backed expertise, soft skills, **DIR**, **CSOT** |
| Skill | `/concierge:pursuit-qualifier` | Qualify an opportunity — **REVI**, proof, team readiness, margin, verdict |
| Skill | `/concierge:pursuit-workspace` | Build a pursuit team and set it up in Concierge: a workspace with the team, client profile and comparable work |
| Skill | `/concierge:rate-benchmark` | Bill rates from comparable work; cost and margin (future feature: pay data) |
| Skill | `/concierge:evidence-bios` | Client-facing consultant bios backed by delivery evidence |
| Skill | `/concierge:account-brief` | Internal client brief before a meeting or QBR |
| Skill | `/concierge:forecast` | Weighted pipeline forecast with **REVI**, against delivery capacity |
| Skill | `/concierge:expansion-plays` | Extensions, renewals, re-engagement and cross-sell targets |
| Skill | `/concierge:sellable-capacity` | Who the firm can sell now, measured with **CSOT** |
| Skill | `/concierge:pipeline` | Open opportunities and resource conflicts |
| Skill | `/concierge:build-proposal` | Client-facing proposal, with evidence-ranked experience |
| Skill | `/concierge:build-game-plan` | Internal delivery game plan |
| Skill | `/concierge:consultant-card` / `consultant-profile` | A consultant's card or full profile (with DIR) as HTML |
| Agent | `@concierge:pipeline-analyst` | Analyses resource allocation (used by `/pipeline`) |
| MCP | `Concierge by ProServe Metrics` | The Concierge MCP server, through its sign-in gateway |

**Concierge's proprietary measures:** DIR (Delivery Impact Rating), CSOT (Capacity Sold Over Time®) and REVI (opportunity
score) — and skills derived from delivery evidence, never self-reported.

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
/concierge:expert-finder solution design, logistics, tomorrow afternoon
/concierge:experience-match enterprise architecture assessment
/concierge:pursuit-qualifier OPP-2024-004
/concierge:pursuit-workspace OPP-2024-004
/concierge:rate-benchmark 2 senior data engineers and an architect
/concierge:evidence-bios EMP0046, EMP0005
/concierge:sellable-capacity next 8 weeks
/concierge:forecast next quarter
```

Most views are internal (DIR, CSOT, rates, costs); share client-facing material through `/concierge:evidence-bios` and
`/concierge:build-proposal`.

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
