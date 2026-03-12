# Agent Gap Detection

## When to run
- When starting a new SDD phase
- When the user asks "what agents am I missing?"
- When a task arises that no installed agent clearly covers

## How to run
1. Read the full agency-agents roster from:
   https://github.com/msitarzewski/agency-agents (README.md index)
2. Read context/project-state.md to understand the current project type
3. Compare installed agents in .claude/agents/ against the full roster
4. Identify agents that are relevant to this project but not installed
5. Present a prioritized list:
   "For your project type, consider adding these agents:"
   - [Agent name] — [why it's relevant] — [source path]
6. Ask: "Want me to install any of these?"
7. If yes — copy from ../agency-agents/ and confirm

## Relevance criteria
- Match agent specialty against: project type, current phase, known stack
- Prioritize agents from: engineering, testing, product, design divisions
- Skip agents clearly irrelevant (e.g. paid-media, spatial-computing)
  unless the project explicitly needs them
