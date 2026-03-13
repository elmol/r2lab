# r2lab

Inspired by R2-D2 — the co-pilot that navigates, guides, and makes everything possible without ever writing the product code itself.

## What is r2lab?

r2lab is an **SDD (Spec Driven Development) orchestrator** that guides software products from discovery to production using [Claude Code](https://docs.anthropic.com/en/docs/claude-code). It's a co-pilot for building products right — **specs first, code second**.

- Orchestrates the full product lifecycle: discovery, story mapping, architecture, feature specs, implementation handoff, review, and launch
- Provides knowledge support for Claude Code best practices: Skills, MCPs, agents, and workflow patterns
- Keeps all artifacts organized in a structured `context/` directory

r2lab does **not** write product code. Implementation happens in the product's own project — r2lab produces the specs and prompts that guide it.

## SDD Workflow

| Phase | Name                   | Goal                                              |
|-------|------------------------|---------------------------------------------------|
| 1     | Discovery              | Understand the problem, users, and constraints    |
| 2     | Story Mapping          | Map user activities, define walking skeleton and scope |
| 3     | Architecture           | Choose tech stack, system boundaries, data model  |
| 4     | Feature Specs          | Write detailed specs per feature/story            |
| 5     | Implementation Handoff | Generate prompts for the real product project     |
| 6     | Review & Validation    | Verify specs against requirements and consistency |
| 7     | Production Readiness   | Final checklist: deploy, monitor, launch plan     |

Phase 2 uses [Jeff Patton's Story Mapping](https://jpattonassociates.com/story-mapping/) approach — the story map replaces the traditional product spec by expressing scope as a visual narrative with horizontal slices (walking skeleton first, then incremental releases).

Each phase has a gate — a checklist that must pass before moving forward.

## Getting Started

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and configured

### Setup

1. Clone this repository:

   ```bash
   git clone https://github.com/elmol/r2lab.git
   cd r2lab
   ```

2. Install the agent definitions from [agency-agents](https://github.com/msitarzewski/agency-agents):

   ```bash
   git clone https://github.com/msitarzewski/agency-agents.git ~/agency-agents
   ```

   Then copy the agents (see [AGENTS.md](AGENTS.md#section-3-installation-guide) for the full list):

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

3. Start Claude Code in the r2lab directory:

   ```bash
   claude
   ```

   r2lab will read `CLAUDE.md`, check for `context/project-state.md`, and guide you from there.

## Usage

Start a new product by running Claude Code inside the r2lab directory. The orchestrator will begin at **Phase 1 (Discovery)** and walk you through each phase one question at a time.

You can also ask knowledge questions at any time:

- "When should I use a Skill vs an Agent vs MCP?"
- "Which MCPs should I configure for a SaaS project?"
- "What are the best practices for structuring CLAUDE.md?"
- "Help me design this handoff prompt" → Prompt Workshop
- "What agents am I missing for this project?" → Agent Gap Detection

## Core Rules

1. **SDD is strict** — no implementation output without an approved spec
2. **Every phase has a gate** — checklist must pass before moving forward
3. **All artifacts live in `context/`** — never outside it
4. **One question at a time** during discovery sessions
5. **Always surface risks and trade-offs explicitly**

## Agent System

r2lab uses agents from the [agency-agents](https://github.com/msitarzewski/agency-agents) catalog plus custom agents. See [AGENTS.md](AGENTS.md) for the full catalog, delegation rules, and installation instructions.

### Built-in capabilities

**Agent Gap Detection** — At the start of each new SDD phase, r2lab scans the agency-agents roster and proactively suggests installing agents that are relevant to your project type but not yet installed. Ask: "What agents am I missing?"

**Prompt Workshop** — A dedicated mode for designing high-stakes prompts before executing them. Runs a structured 4-question discovery process, validates the draft against a quality checklist, and offers a library of reusable patterns (handoff, multi-agent, migration, API, UI component). Also diagnoses and patches prompts that didn't work as expected.

**AI Dev Workflow** — Defines how Claude Code and AI agents collaborate across the development lifecycle: writer-reviewer pair programming cycles, CI/CD quality gates (lint, test, integration, e2e, security review, code review), automated bugfixing workflows, release strategy with SemVer, and recommended skills (Trail of Bits security skills, `claude-code-security-review`, Claude Code Review).

### Project structure

```
r2lab/
├── CLAUDE.md                  # Main instructions for Claude Code
├── AGENTS.md                  # Agent catalog and delegation rules
├── workflow/
│   └── agent-gap-detection.md # Agent gap detection process
├── context/
│   ├── project-state.md       # Session state (created on first run)
│   ├── discovery.md
│   ├── story-map.md           # Patton's Story Mapping (replaces product-spec)
│   ├── architecture.md
│   └── specs/
│       └── [feature-name].spec.md
└── .claude/
    └── agents/                # Agent definitions
        ├── prompt-workshop.md # Custom: prompt design and diagnosis
        ├── ai-dev-workflow.md # Custom: AI-assisted development workflow
        └── [agency-agents]    # Installed from agency-agents repo
```

## License

MIT
