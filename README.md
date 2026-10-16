# Concierge plugins for Claude Code

The ProServe Metrics marketplace for Claude Code.

```
/plugin marketplace add ProServ-Metrics/concierge-claude-plugin
/plugin install concierge@concierge-demo
```

| Plugin | What it does |
|---|---|
| [`concierge`](plugins/concierge/README.md) | Sales and pipeline work on Concierge data |
| [`concierge-foundry`](plugins/concierge-foundry) | Staffing through Azure AI Foundry hosted agents (legacy) |

Both connect to the Concierge MCP server through its sign-in gateway: after installing, run `/mcp`, select the
Concierge server and choose **Authenticate**.
