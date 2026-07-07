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
| Agent | `@concierge:talent-scout` | Researches candidates for one role (spawned by `/staff`) |
| Agent | `@concierge:team-evaluator` | Evaluates a lineup (spawned by `/staff` and `/evaluate-lineup`) |
| Agent | `@concierge:pipeline-analyst` | Analyses resource allocation (spawned by `/pipeline`) |
| MCP | `Consultants` | Connects to `Concierge.Services.MCP` at `http://localhost:5000` |

---

## Prerequisites

1. **Claude Code** (Desktop app or CLI) — download from [claude.ai/download](https://claude.ai/download)
2. **Concierge MCP server** running locally (see below)
3. **Claude Code v2.1.198+** recommended (background subagents run concurrently by default)

---

## Step 1 — Start the MCP server

From the `moneyball-services` directory:

```bash
dotnet run --project MCP/Concierge.Services.MCP.csproj
```

The server starts on `http://localhost:5000` by default.

**To run without authentication** (for local development), set in `appsettings.Development.json`:
```json
{
  "Authorization": {
    "Enabled": false
  }
}
```

**To run with Entra ID authentication** (production), the server will return OAuth metadata on first connection. Claude Code will open a browser window to authenticate. Use your org credentials.

Verify the server is reachable:
```bash
curl -i http://localhost:5000
# Should return 200 or 401 (not connection refused)
```

---

## Step 2 — Load the plugin

### Option A: CLI (recommended for dev)

```bash
cd /path/to/moneyball-services
claude --plugin-dir ./Plugins/Claude
```

### Option B: Desktop app

1. Open Claude Desktop → Code tab
2. Click **+** → **Plugins** → **Add plugin**
3. Browse to `moneyball-services/Plugins/Claude`

### Option C: Install to personal plugins (persists across sessions)

```bash
claude plugin install ./Plugins/Claude
```

---

## Step 3 — Verify the plugin loaded

In Claude Code, run:
```
/help
```
You should see the `concierge` namespace with all 5 skills listed.

Or check plugins directly:
```
/plugin list
```

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

The Consultants MCP server is not reachable. Check:
- Server is running: `curl -i http://localhost:5000`
- Port matches `.mcp.json` (default: 5000)
- If using auth, Entra ID credentials are valid

To change the MCP server URL, edit `.mcp.json`:
```json
{
  "mcpServers": {
    "Consultants": {
      "type": "http",
      "url": "http://your-server:port"
    }
  }
}
```
Then run `/reload-plugins`.

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
- The OAuth flow requires a browser — ensure Claude Desktop has permission to open one
- Check that `Authorization:Enabled` in the server's appsettings matches your environment

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
└── .mcp.json                ← Consultants server at localhost:5000
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
