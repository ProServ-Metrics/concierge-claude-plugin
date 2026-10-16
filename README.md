# Concierge Claude Plugin

An installable Claude Code plugin for AI-powered staffing and resource management. Connects to the Concierge MCP server and exposes skills for staffing engagements, finding candidates, evaluating lineups, and reviewing the pipeline.

---

## What's included

| Component | Name | Purpose |
|-----------|------|---------|
| Skill | `/concierge:staff` | Full staffing workflow — spawns parallel talent scouts, collects lineup, evaluates |
| Skill | `/concierge:find-candidates` | Quick candidate search by skills or role |
| Skill | `/concierge:evaluate-lineup` | Comprehensive team evaluation |
| Skill | `/concierge:check-availability` | Consultant availability lookup |
| Skill | `/concierge:pipeline` | Pipeline overview and resource conflict analysis |
| Skill | `/concierge:account-brief` | Sales: internal client brief before a meeting or QBR |
| Skill | `/concierge:deal-review` | Sales: qualify an opportunity — staffing readiness, estimated margin, verdict |
| Skill | `/concierge:forecast` | Sales: weighted pipeline forecast vs. delivery capacity |
| Skill | `/concierge:expansion-plays` | Sales: extensions, renewals, re-engagement and cross-sell targets |
| Skill | `/concierge:sellable-capacity` | Sales: what the bench can take on, by skill and start date |
| Agent | `@concierge:talent-scout` | Researches candidates for one role (spawned by `/staff`) |
| Agent | `@concierge:team-evaluator` | Evaluates a lineup (spawned by `/staff` and `/evaluate-lineup`) |
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

## Testing each skill

### Full staffing workflow

```
/concierge:staff
```

Or with an opportunity ID:
```
/concierge:staff OP-2024-001
```

**Expected flow:**
1. Claude fetches the opportunity (or shows a list to pick from)
2. Claude spawns one talent scout per role simultaneously (check the **Tasks pane** in Desktop to see them running in parallel)
3. Scouts return candidate lists (typically 30–90 seconds depending on role count)
4. Claude presents a candidate table per role
5. You select one candidate per role in natural language
6. Claude evaluates the lineup and returns a full assessment

### Find candidates

```
/concierge:find-candidates Python developer with AWS experience
```

### Check availability

```
/concierge:check-availability available ML engineers next 4 weeks
```

### Evaluate a lineup

```
/concierge:evaluate-lineup
```
Then describe the lineup when prompted, or paste role assignments.

### Pipeline overview

```
/concierge:pipeline
```

Or for a specific opportunity:
```
/concierge:pipeline OP-2024-001
```

---

### Sales skills

```
/concierge:account-brief Atlantic Shipping Co
/concierge:deal-review OPP-2024-002
/concierge:forecast next quarter
/concierge:expansion-plays
/concierge:sellable-capacity next 8 weeks
```

All five are internal views (they include ratings, costs or individual availability) — share client-facing material through `/concierge:build-proposal` instead.

## Watching parallel scouts run (Desktop)

In the Claude Desktop app:
1. Open **Views → Tasks** to show the tasks pane
2. Run `/concierge:staff` on a multi-role opportunity
3. You will see N background subagents appear simultaneously, one per role
4. Each shows status (running → complete) independently

This confirms the parallel execution is working.

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
Plugins/Claude/
├── .claude-plugin/
│   └── plugin.json          ← manifest (name, version)
├── skills/
│   ├── staff/SKILL.md       ← /concierge:staff
│   ├── find-candidates/SKILL.md
│   ├── evaluate-lineup/SKILL.md
│   ├── check-availability/SKILL.md
│   └── pipeline/SKILL.md
├── agents/
│   ├── talent-scout.md      ← @concierge:talent-scout (haiku model)
│   ├── team-evaluator.md    ← @concierge:team-evaluator
│   └── pipeline-analyst.md  ← @concierge:pipeline-analyst
├── hooks/
│   └── hooks.json           ← PostToolUse: MCP error hint
└── .mcp.json                ← the Concierge MCP server (through its sign-in gateway)
```

---

## Architecture notes

### Why no proxy layer?

The `llm-proxy-service` (Node.js) handles streaming, auth, and tool loop management for the web app. The Claude Code plugin skips this entirely — Claude Code has its own:
- Native MCP tool execution
- OAuth 2.0 flow for MCP servers
- Streaming output in the chat UI
- Agentic loop (retry, tool use, multi-turn)

### How parallel scouts work

`/concierge:staff` instructs Claude to call the Task tool N times in a single response (once per role), each with `agent: "concierge:talent-scout"`. Claude Code v2.1.198+ runs these as background subagents concurrently. Each scout has its own context window and independently calls the Consultants MCP tools. Results return asynchronously and Claude synthesizes them once all complete.

This is the same pattern as the Node.js orchestrator but running natively inside Claude Code — no separate backend required.

### Agent teams vs background subagents

The plugin uses **background subagents** (available in Desktop and CLI). True **agent teams** (with inter-agent messaging) are CLI-only. For this use case, background subagents are sufficient since scouts don't need to communicate with each other — they work independently and report back to the orchestrator.
