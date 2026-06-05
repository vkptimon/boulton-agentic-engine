---
name: agent
description: Implement, fix, refactor, or validate code when the user explicitly wants changes. Use for building features, fixing bugs, creating files, wiring frontend and backend, adding tests, running validation. Trigger phrases: implement, fix, create, add, refactor, update, wire up, run tests, make this work.
---

# Agent Mode

For **execution** — only when the user explicitly asked.

## Guardrail
If the user only asked a question or a plan, do not use this skill. Step back to `/ask` or `/plan`.

## Learning-first (non-trivial changes)
Before writing any files:
1. Read the relevant existing code and explain what it currently does.
2. State at least two implementation approaches and the tradeoff between them.
3. Say which approach you are taking and why.
4. Wait for the user to confirm before writing anything.

## Procedure
1. Confirm implementation is explicitly requested.
2. Gather context before editing.
3. Make the smallest viable change set.
4. Preserve readability and existing conventions.
5. Validate with relevant checks when practical.

## Quality bar
- Keep diffs tight and purposeful.
- No speculative abstractions or features not asked for.
- Business logic isolated from transport / UI glue.
- Backend: thin FastAPI handlers, pure functions for parsing/scoring.
- Frontend: focused typed Vue components, explicit async/error states.
- Resume domain: preserve user voice, no fabricated metrics, only touch allowed sections.

## Required output after every implementation

### What changed
One sentence.

### Why this design
The structural reasoning — not just "it works". What constraint, tradeoff, or architectural principle drove the choice.

### Learning
The system design principle, programming paradigm, language feature, or theoretical concept this embodies. Name it explicitly (e.g., *separation of concerns*, *pure function*, *optimistic UI*, *idempotency*, *single responsibility*, *layered architecture*).

### Broader applicability
One other context where this same concept applies.
