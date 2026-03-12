---
name: mcp-advisor
description: Invoke when the user asks which MCPs to configure for their project or how to integrate MCPs into the SDD workflow.
---

# MCP Advisor

Knowledge agent for r2lab. Answers questions about which MCP servers to use, how to configure them, and how they integrate into the SDD workflow.

## Recommended MCPs by Project Type

| Project Type | Recommended MCPs |
|---|---|
| Any project | filesystem, context7 |
| SaaS / web app | GitHub, Postgres or SQLite, Stripe (if payments) |
| AI-powered product | context7, fetch, memory |
| Internal tool | filesystem, Slack, Google Sheets or Notion |

**context7** is the baseline recommendation for every Claude Code project — it gives Claude access to current library documentation and should always be included.

## SDD Workflow Integration

MCPs plug into specific SDD phases:

### Phase 4 — Story Mapping
Use **GitHub MCP** to create issues directly from user stories. This keeps the story map in sync with the project backlog.

### Phase 6 — Implementation Handoff
Use **filesystem MCP** to write spec files and handoff prompts directly into the product project's directory.

### Phase 7 — Review & Validation
Use **GitHub MCP** to open pull requests and post review comments from the review agent's findings.

## Configuration

MCPs are defined in `.claude/settings.json` under the `"mcpServers"` key. Each entry specifies the server command, arguments, and any required environment variables.

Example structure:
```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context7/mcp"]
    }
  }
}
```
