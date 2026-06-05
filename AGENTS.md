# Antigravity Agent Operating Guide

Three working modes: **ask** (answer only), **plan** (investigate, no code), **agent** (implement on explicit request).

> **When intent is ambiguous, choose the least invasive mode. Default to ask.**

## Skills

Slash commands for Antigravity IDE and CLI (auto-discovered from `.agents/skills/`):

- `/mode-selector` — classify a request before acting
- `/ask` — answer and explain, no file edits
- `/plan` — produce a change plan, no implementation
- `/agent` — implement when the user explicitly asks

VS Code fallback: `.github/skills/<name>/SKILL.md`

## Intent classification

Classify in this order before every action:
1. Question / explanation / comparison / review only? → **ask**
2. Roadmap / breakdown / architecture / strategy? → **plan**
3. Explicit "implement / fix / create / change / wire up"? → **agent**

**Override rule**: "just explain", "no implementation", "question only", "don't change code" → **ask**, regardless of everything else.

Mixed signals: follow the highest-confidence explicit intent.

## Learning-first constraint

Before implementing anything non-trivial:
1. Read the relevant existing code and explain what it currently does.
2. State at least two implementation approaches and the tradeoff between them.
3. Say which approach you are taking and why.
4. Wait for the user to confirm before writing any files.

Do not skip straight to code. Understanding is the goal.

## Post-implementation knowledge transfer

After every implementation, always include a **Learning** section:

- **What was built**: one sentence.
- **Why this design**: the structural reasoning — not just "it works".
- **Concept**: the system design principle, programming paradigm, language feature, or theoretical idea embodied. Name it explicitly (e.g. *separation of concerns*, *pure function*, *idempotency*, *layered architecture*, *optimistic UI*, *single responsibility*, *event-driven design*).
- **Broader applicability**: one other context where this same concept is useful.

## Context rules

- Read only files relevant to the current task.
- `high level idea.md` and `project-scope.md` are the product source of truth.
- Respect current codebase maturity — don't design as if unbuilt modules already exist.
- If information is missing, say so; do not guess.

## Code quality

**Backend (FastAPI + Python)**
- Keep route handlers thin; business logic goes in focused modules.
- Deterministic logic (parsing, scoring) stays isolated from LLM logic.
- Use clear data contracts and explicit return shapes.
- Prefer small pure functions for parsing, scoring, and transformations.

**Frontend (Vue 3 + TypeScript)**
- Keep components focused with explicit props/events and typed state.
- Build minimal UI first; add polish without obscuring structure.
- Keep async/loading/error states explicit.

**Resume domain**
- Preserve the user's original voice in all suggestions.
- Never fabricate metrics, achievements, or facts.
- Only touch Work Experience, Projects, and Technical Skills unless explicitly told otherwise.
- Favor minimal edits over full rewrites.

## Performance and readability

- Simple > clever. Prefer readable architecture over framework complexity.
- Avoid speculative abstractions and deep inheritance.
- Descriptive names, straightforward control flow, cohesive modules.
