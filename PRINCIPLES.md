# r2lab — Working Principles

Learned principles from real SDD cycles. These define how r2lab guides development.

---

## 1. r2lab Never Implements

r2lab produces **specs, stories, and handoff prompts**. It never creates repos, writes code, or runs implementation agents outside its own `context/` directory.

- The handoff prompt is the final deliverable — the user takes it to another Claude Code session
- When the user says "let's start" about implementation, it means "generate the handoff prompt"
- r2lab only creates files inside its own repo (`context/specs/`, `context/handoff/`, etc.)

---

## 2. Phases Are Iterative, Not Sequential

The 7 SDD phases are **tools, not a linear pipeline**. In practice:

- Phases 4 (Feature Specs) and 5 (Implementation Handoff) run together iteratively
- Phase 6 (Review) can be invoked at any point — after infra, after a few stories, or after a full slice
- The iteration scope varies: sometimes one phase, sometimes spanning multiple phases
- The product emerges through iteration, not through completing phases one by one

Example: Write infra specs (Phase 4) → implement them (Phase 5) → formal review (Phase 6) → fix findings → then move to functional specs. The phases serve the work, not the other way around.

---

## 3. Specs Must Be Minimal

One concern per spec. When in doubt, the spec is too big — split it.

- Don't bundle infrastructure with functional (repo setup ≠ CI ≠ functional story)
- Don't create specs for code that has no consumer yet — let it emerge from the story that needs it
- Architectural decisions go in ADRs, not in functional specs
- Let infrastructure emerge from what's needed, not from a target-state blueprint

---

## 4. Non-Functional Specs Come First

The story map (Phase 2) only has functional user stories. But implementation requires non-functional specs too.

Before the first functional spec, create infrastructure specs for:
- Repository setup
- CI/CD pipeline
- AI workflow rules (CLAUDE.md, REVIEW.md)
- Seed ADRs (architectural decisions)
- Development practices

Each infra spec must unblock something — if it doesn't, defer it. Security review, release pipeline, advanced tooling → add when there's code to use them on.

---

## 5. The Virtuous Loop

Specs and implementation feed each other:

```
r2lab (spec) → product repo (implement) → review → feedback → r2lab (improve specs)
```

- Don't wait to write all specs before starting implementation
- Approve a spec → implement → review results → adjust future specs
- Implementation feedback reveals when a spec was too big, too vague, or missing something

---

## 6. The Handoff Mechanism

How specs flow from r2lab to the product repo:

1. **First handoff creates the product's CLAUDE.md** — project rules, ADR rules, conventional commits, spec-first development
2. **Specs are copied** to the product repo in `docs/specs/` — they persist as reference
3. **Stories are copied** to the product repo in `docs/stories/` — acceptance criteria and context
4. **Architecture is copied** to the product repo in `docs/architecture.md`
5. **Handoff prompts** are concise — they reference specs/stories already in the repo

Knowledge must persist in the implementation repo. If specs only exist in r2lab, each Claude Code session starts without context.

---

## 7. Artifact Synchronization

Artifacts must stay in sync between r2lab and the product repo:

| Artifact | Source of truth | Direction |
|----------|----------------|-----------|
| Stories | r2lab | r2lab → product repo |
| Specs | r2lab | r2lab → product repo |
| Architecture | r2lab | r2lab → product repo |
| ADRs | product repo | Created during implementation |
| INDEX.md | r2lab | Status updated after implementation |

Current approach: manual sync at every handoff step.

---

## 8. Discuss Before Formalizing

Don't rush to close phases. Brainstorm back and forth before committing to artifacts. When agents drift into technical details during product-focused phases, redirect them. The user's judgment shapes the process — r2lab proposes, the user decides.

---

## 9. Horizontal Stories, Vertical Specs

User stories are **horizontal** — each one cuts across all layers (device, contracts, attester) delivering thin end-to-end value. Stories are flat with no hierarchy. When sub-slicing, original stories are replaced by smaller horizontal stories, each owning its own acceptance criteria.

Specs are **vertical** — tiny, one concern each (one component, one capability). Multiple vertical specs implement a single horizontal story.

- **Horizontal stories:** S1a.1 (register + confirm), S1a.2 (VERIFIED), S1a.3 (UNVERIFIED)
- **Vertical specs per story:** V1 (contract), V2 (device CLI), V3 (attester CLI), V4 (e2e validation)

Sub-slices (1a, 1b, 1c) are horizontal passes of increasing depth:
- **1a:** Hardcode everything, prove the chain works
- **1b:** Real crypto, emulation, error handling
- **1c:** Edge cases, CI, polish

Avoid premature abstractions (e.g., don't create a `common/` crate in 1a when hardcoded values suffice). Let shared code emerge from actual duplication, not from anticipated need.
