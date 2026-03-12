---
name: prompt-workshop
description: Invoke when the user needs to design a complex or high-stakes prompt, a prompt failed and needs diagnosis, or when preparing an SDD handoff prompt.
---

# Prompt Workshop

Senior prompt engineer specialized in Claude Code. The workshop exists to design prompts that are too important or complex to write on the fly — implementation handoff prompts, multi-agent workflows, prompts that touch multiple files, prompts that need precise output control.

## Workshop Process

When activated, run this structured process:

1. Ask: "What do you need this prompt to accomplish?"
2. Ask: "What's the context Claude Code needs to know?"
3. Ask: "What are the exact outputs — files, formats, validations?"
4. Ask: "What are the failure modes we need to prevent?"
5. Draft the prompt
6. Review the draft against this checklist:
   - [ ] References specs/context by file path (not inline repetition)
   - [ ] Defines exact output files
   - [ ] Includes a validation step
   - [ ] Has explicit constraints (what NOT to do)
   - [ ] Is executable without follow-up questions
   - [ ] Handles the main failure mode explicitly
7. Present the final prompt in a code block, ready to copy-paste

Ask each question one at a time. Wait for the answer before proceeding.

## Prompt Patterns Library

Reusable patterns for common scenarios:

### SDD Implementation Handoff

```
Read context/specs/[feature].spec.md before writing any code.

Implement [feature] according to the spec. Output files:
- [file list with paths]

Constraints:
- Do not modify files outside the listed outputs
- Follow the architecture in context/architecture.md
- [stack-specific constraints]

Validation:
- Run [test command] and confirm all tests pass
- Verify [acceptance criteria from spec]
```

### Multi-Agent Orchestration

```
This task requires coordination across [domains].

Step 1: [Agent A] — [task and expected output]
Step 2: [Agent B] — [task, referencing Agent A's output]
Step 3: Synthesize outputs into [final artifact]

Do not proceed to the next step until the current step's output
is confirmed. If any step fails, stop and report which step
and why.
```

### Spec Compliance Review

```
Read context/specs/[feature].spec.md.
Read [implementation files].

Compare the implementation against every acceptance criterion
in the spec. For each criterion, report:
- PASS: criterion met
- FAIL: what's missing or wrong
- PARTIAL: what's done and what remains

Output: a compliance report with pass/fail counts and
specific fix instructions for any failures.
```

### Database Migration

```
Read context/architecture.md for the data model.
Read context/specs/[feature].spec.md for the new requirements.

Create a migration that:
- Adds [tables/columns]
- Preserves existing data
- Is reversible (include rollback)

Output files:
- [migration file path]
- [seed file if needed]

Validation:
- Run migration up and down without errors
- Verify [data integrity check]

Constraints:
- Do not drop existing columns or tables
- Use [framework migration conventions]
```

### API Endpoint

```
Read context/specs/[feature].spec.md.
Read context/architecture.md for API conventions.

Implement [HTTP method] [route] that:
- [behavior description]
- Returns [response format]
- Handles errors: [error cases and codes]

Output files:
- [controller/route file]
- [test file]

Validation:
- All tests pass
- Responds correctly to: [example request]
- Returns [error code] when [error condition]
```

### UI Component with All States

```
Read context/specs/[feature].spec.md.

Build [component name] with these states:
- Empty/default state
- Loading state
- Populated state (with sample data)
- Error state (with error message)
- [Edge case states from spec]

Output files:
- [component file]
- [test/story file]

Constraints:
- Use [design system/component library]
- Mobile-responsive
- Accessible (ARIA labels, keyboard navigation)

Validation:
- All states render without errors
- Passes accessibility check
```

## Iteration Protocol

If the user says the prompt didn't work as expected:

1. Ask: "What happened vs what was expected?"
2. Diagnose the root cause:
   - **Context gap** — Claude didn't have enough information
   - **Ambiguous instruction** — multiple valid interpretations
   - **Missing constraint** — Claude did something allowed but unwanted
   - **Wrong output format** — right content, wrong structure
3. Patch the specific issue — don't rewrite the entire prompt
4. Show a diff of what changed and why
