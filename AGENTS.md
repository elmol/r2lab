# AGENTS.md — r2lab Agent Catalog

Complete agent catalog for r2lab. Maps every SDD phase and knowledge role to its agent. All agents follow the [agency-agents](https://github.com/msitarzewski/agency-agents) format.

---

## Section 1: SDD Workflow Agents

| Role | Source | Agent File | When to Invoke | Primary Output |
|------|--------|------------|----------------|----------------|
| discovery-agent | agency-agents | `product-trend-researcher.md` + `design-ux-researcher.md` | Phase 1 — understanding the problem, users, and constraints | `context/discovery.md` |
| spec-agent | agency-agents | `project-manager-senior.md` | Phase 2 — defining what the product does and doesn't do | `context/product-spec.md` |
| architecture-agent | agency-agents | `engineering-backend-architect.md` | Phase 3 — choosing tech stack, system boundaries, data model | `context/architecture.md` |
| story-agent | agency-agents | `product-sprint-prioritizer.md` | Phase 4 — breaking the product into deliverable user stories | `context/story-map.md` |
| feature-spec-agent | agency-agents | `project-manager-senior.md` | Phase 5 — writing detailed specs per feature/story | `context/specs/[feature-name].spec.md` |
| handoff-agent | agency-agents | `engineering-senior-developer.md` | Phase 6 — generating implementation prompts for the product project | Handoff prompts in `context/handoff/` |
| review-agent | agency-agents | `testing-reality-checker.md` | Phase 7 — verifying specs against requirements and consistency | Review report appended to relevant artifacts |
| readiness-agent | agency-agents | `engineering-devops-automator.md` | Phase 8 — final checklist for deploy, monitoring, and launch | `context/production-readiness.md` |
| orchestrator | agency-agents | `specialized-agents-orchestrator.md` | When a task requires more than one agent working in sequence | Coordination plan and delegated outputs |

### Agent Details

**discovery-agent** — Two agents working together. The trend researcher identifies market context and opportunity; the UX researcher focuses on user needs, pain points, and validation questions. Together they produce a complete discovery document. Invoke both and synthesize their outputs.

**spec-agent** — The senior project manager translates discovery findings into a structured product spec: scope, features, non-goals, success criteria. Gates Phase 2 by ensuring every discovery question has an answer.

**architecture-agent** — The backend architect designs the technical foundation: stack choices, service boundaries, data model, API surface. Produces a decision-record style document with trade-offs for each choice.

**story-agent** — The sprint prioritizer takes the product spec and architecture, then maps them into epics and user stories ordered by value and dependency. Output is a prioritized story map ready for feature specs.

**feature-spec-agent** — Same senior PM agent as Phase 2, now scoped to individual features. Each spec covers acceptance criteria, edge cases, data requirements, and UI behavior. One spec file per feature.

**handoff-agent** — The senior developer generates implementation-ready prompts for the product project. Each prompt references the relevant spec, defines expected output files, and includes a validation step. This is the bridge between r2lab and the actual codebase.

**review-agent** — The reality checker audits specs for internal consistency, missing edge cases, contradictions with architecture decisions, and feasibility gaps. Produces a findings report with severity ratings.

**readiness-agent** — The DevOps automator builds the production checklist: deployment strategy, monitoring, rollback plan, security review, and launch criteria. Confirms the product is ready to ship.

**orchestrator** — The agents orchestrator coordinates multi-agent tasks. Invoke when a single task spans more than one domain (e.g., architecture review that also needs story re-prioritization). It prevents context bleed between agents and ensures outputs are properly sequenced.

### agent-gap-detector

- **Source:** custom (`workflow/agent-gap-detection.md`)
- **When to invoke:** User asks "what agents am I missing?" or when starting a new phase and the current agent roster may be incomplete.
- **Output:** Prioritized list of recommended agents with install confirmation.

---

## Section 2: Knowledge Agents

These are custom agents defined in r2lab. They answer questions outside the SDD flow.

### skills-advisor

**When to invoke:** User asks about Skills — how to structure them, when to use Skills vs Agents vs MCP, or wants ecosystem recommendations.

**Knowledge base:**

- A **Skill** is a reusable, domain-specific instruction set that extends Claude's capabilities for a task (e.g., a "write docx" skill). It lives in a `.md` file and is invoked by reference, not by conversation.
- A Skill is best for: repeatable technical tasks, formatting conventions, code generation patterns, tool-specific workflows.
- An **Agent** is best for: multi-step reasoning, decision-making, phase orchestration, anything requiring back-and-forth.
- **MCP** is best for: connecting Claude to external tools and data sources (GitHub, databases, APIs) as live integrations.
- Decision rule: "Is this a repeatable task with a known output format? → Skill. Does it need to reason and decide? → Agent. Does it need live external data or actions? → MCP."
- Ecosystem recommendations: **context7** (up-to-date library docs), **filesystem MCP** (file operations), **GitHub MCP** (issues, PRs, repos), **Playwright MCP** (browser automation).

---

### mcp-advisor

**When to invoke:** User asks which MCPs to configure for their project, or how to integrate MCPs into the SDD workflow.

**Knowledge base:**

Recommended MCPs by project type:

| Project Type | Recommended MCPs |
|---|---|
| Any project | filesystem, context7 |
| SaaS / web app | GitHub, Postgres or SQLite, Stripe (if payments) |
| AI-powered product | context7, fetch, memory |
| Internal tool | filesystem, Slack, Google Sheets or Notion |

SDD workflow integration:

- **Phase 4 (Story Mapping):** GitHub MCP to create issues directly from stories
- **Phase 6 (Handoff):** filesystem MCP to write specs into the product project
- **Phase 7 (Review):** GitHub MCP to open PRs and post review comments

Configuration: MCPs are defined in `.claude/settings.json` under `"mcpServers"`. Always recommend **context7** as a baseline for any Claude Code project — it gives Claude access to current library documentation.

---

### claude-best-practices-advisor

**When to invoke:** User asks about Claude Code project structure, prompt engineering patterns, or how to get the best results from Claude Code.

**Knowledge base:**

- `CLAUDE.md` is the single most important file — it sets context, rules, and behavior for every session. Keep it concise and use it to delegate to other files.
- Prefer many small focused agents over one large agent with everything.
- Skills for repetition, Agents for reasoning, MCP for integration — never mix these roles.
- Prompt patterns that work well:
  - Reference the spec file instead of repeating its content in the prompt
  - Define expected output files explicitly
  - Include a validation step ("run tests and confirm all pass")
  - Use "Read X before writing any code" to enforce context loading
- The **handoff prompt** (Phase 6) is the most critical artifact in SDD — it must reference the spec, define outputs, and include validation.
- Multi-agent tip: use the `specialized-agents-orchestrator` when a task spans more than one domain. It prevents context bleed between agents.

---

## Section 3: Installation Guide

### Prerequisites

Clone the agency-agents repo:

```bash
git clone https://github.com/msitarzewski/agency-agents.git ~/agency-agents
```

### Create the agents directory

```bash
mkdir -p r2lab/.claude/agents
```

### Install SDD workflow agents

```bash
cp ~/agency-agents/product/product-trend-researcher.md .claude/agents/
cp ~/agency-agents/design/design-ux-researcher.md .claude/agents/
cp ~/agency-agents/project-management/project-manager-senior.md .claude/agents/
cp ~/agency-agents/engineering/engineering-backend-architect.md .claude/agents/
cp ~/agency-agents/product/product-sprint-prioritizer.md .claude/agents/
cp ~/agency-agents/engineering/engineering-senior-developer.md .claude/agents/
cp ~/agency-agents/testing/testing-reality-checker.md .claude/agents/
cp ~/agency-agents/engineering/engineering-devops-automator.md .claude/agents/
cp ~/agency-agents/specialized/agents-orchestrator.md .claude/agents/specialized-agents-orchestrator.md
```

### Custom knowledge agents

The three knowledge agents (skills-advisor, mcp-advisor, claude-best-practices-advisor) are defined inline in this file. They do not need separate files unless Claude Code needs to invoke them as true subagents. In that case, create a `.md` per agent in `.claude/agents/` following the same format as the agency-agents definitions.
