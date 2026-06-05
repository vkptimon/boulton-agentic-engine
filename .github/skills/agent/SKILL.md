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

## Procedure
1. Confirm the request requires implementation.
2. Gather the relevant context before editing.
3. Break the work into clear steps.
4. Make the smallest viable set of changes.
5. Preserve readability and existing conventions.
6. Validate with relevant checks when practical.
7. Summarize exactly what changed and how it was verified.

## Quality bar
- Prefer simple, maintainable code.
- Keep diffs tight and purposeful.
- Do not introduce speculative abstractions.
- Keep business logic isolated from transport/UI glue.
- Respect project constraints from the repo docs.

## Repo-specific reminders
- Backend: keep FastAPI route handlers thin.
- Frontend: keep Vue components focused and typed.
- Resume suggestions: preserve user voice, avoid fabricated metrics, and limit edits to allowed resume sections unless the user expands scope.

## Avoid
- Starting implementation without enough context
- Making broad rewrites for a small request
- Skipping validation when a quick check is available
- Continuing to code after the user asked only for explanation
