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

## 9. Horizontal Slicing Over Vertical Stories

User stories are vertical (one feature end-to-end). But implementation should be sliced **horizontally** — thin end-to-end passes through all layers. Each horizontal slice is a walking skeleton that gets thicker with each iteration.

- **Slice 1a:** Hardcode everything, prove the chain works (no real crypto, no error handling, fake data)
- **Slice 1b:** Replace shortcuts with real behavior (real crypto, emulation mode, validation)
- **Slice 1c:** Edge cases, CI integration, polish

This means specs are per **sub-slice**, not per story. A sub-slice touches multiple stories partially. The stories remain as reference for full acceptance criteria, but implementation is organized by horizontal layers.

Avoid premature abstractions (e.g., don't create a `common/` crate in 1a when hardcoded values suffice). Let shared code emerge from actual duplication, not from anticipated need.
