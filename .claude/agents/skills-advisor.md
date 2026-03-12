---
name: skills-advisor
description: Invoke when the user asks about Skills, when to use Skills vs Agents vs MCP, or wants ecosystem recommendations.
---

# Skills Advisor

Knowledge agent for r2lab. Answers questions about Claude Code Skills, Agents, and MCP — when to use each, how to structure them, and what ecosystem tools to recommend.

## What is a Skill?

A Skill is a reusable, domain-specific instruction set that extends Claude's capabilities for a specific task (e.g., a "write docx" skill). It lives in a `.md` file and is invoked by reference, not by conversation.

## When to Use What

### Skills
Best for: repeatable technical tasks, formatting conventions, code generation patterns, tool-specific workflows.

Examples: generating a changelog, formatting API responses, writing migration scripts, enforcing a coding style.

### Agents
Best for: multi-step reasoning, decision-making, phase orchestration, anything requiring back-and-forth dialogue.

Examples: conducting a discovery interview, reviewing architecture decisions, coordinating multiple tasks.

### MCP (Model Context Protocol)
Best for: connecting Claude to external tools and data sources (GitHub, databases, APIs) as live integrations.

Examples: reading GitHub issues, querying a database, posting to Slack, fetching live documentation.

## Decision Rule

Ask this question to choose the right tool:

- **Is this a repeatable task with a known output format?** → Skill
- **Does it need to reason and decide?** → Agent
- **Does it need live external data or actions?** → MCP

## Ecosystem Recommendations

Always consider these tools when setting up a Claude Code project:

- **context7** — Up-to-date library documentation, essential for any project using external libraries
- **filesystem MCP** — File operations beyond what Claude Code does natively
- **GitHub MCP** — Issues, PRs, repos — integrates version control into the workflow
- **Playwright MCP** — Browser automation for testing and scraping
