---
name: agent
description: 'Implement, fix, refactor, or validate code when the user explicitly wants changes. Use for building features, fixing bugs, creating files, wiring frontend and backend, adding tests, and running relevant validation. Trigger phrases: implement, fix, create, add, refactor, update, wire up, run tests, make this work.'
argument-hint: 'Describe the change to implement.'
user-invocable: true
---

# Agent Mode

This skill is for **execution**.

## When to use
- The user explicitly wants code changes
- A bug needs to be fixed
- A feature needs to be built
- Files need to be created or updated
- Tests or validation need to be run after changes

## Guardrail before starting
If the user only asked a question or requested a plan, do **not** use this skill.
When in doubt, step back to **ask** or **plan**.

## Learning-first (non-trivial changes)
Before writing any files:
1. Read the relevant existing code and explain what it currently does.
2. State at least two implementation approaches and the tradeoff between them.
3. Say which approach you are taking and why.
4. Wait for the user to confirm before writing anything.

Do not skip straight to code. Understanding is the goal.

## Procedure
1. Confirm implementation is explicitly requested.
2. Gather the relevant context before editing.
3. Break the work into clear steps.
4. Make the smallest viable set of changes.
5. Preserve readability and existing conventions.
6. Validate with relevant checks when practical.

## Quality bar
- Keep diffs tight and purposeful.
- Do not introduce speculative abstractions.
- Keep business logic isolated from transport/UI glue.
- Backend: thin FastAPI handlers, pure functions for parsing/scoring.
- Frontend: focused typed Vue components, explicit async/error states.
- Resume domain: preserve user voice, no fabricated metrics, only touch allowed sections.

## Required output after every implementation

### What changed
One sentence.

### Why this design
The structural reasoning — not just "it works". What constraint, tradeoff, or architectural principle drove the choice.

### Learning
The system design principle, programming paradigm, language feature, or theoretical concept this embodies. Name it explicitly (e.g. *separation of concerns*, *pure function*, *optimistic UI*, *idempotency*, *single responsibility*, *layered architecture*).

### Broader applicability
One other context where this same concept applies.
