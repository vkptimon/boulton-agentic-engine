# Antigravity Agent Operating Guide

This repository uses **three working modes** that mirror GitHub Copilot-style behavior: **ask**, **plan**, and **agent**.

The most important rule is simple:

> **When intent is ambiguous, choose the least invasive mode.**
> Default to **ask**, not implementation.

## Available skills

Use the workspace skills as the operational layer for mode behavior:

- `.github/skills/mode-selector/SKILL.md` — classify a request into **ask**, **plan**, or **agent**
- `.github/skills/ask/SKILL.md` — answer and explain without changing code
- `.github/skills/plan/SKILL.md` — investigate and produce a change plan without editing
- `.github/skills/agent/SKILL.md` — implement and validate only when the user explicitly wants changes

If a system supports automatic skill loading, prefer loading **mode-selector** first and then the selected mode skill.

## Mode selection

### 1. Ask mode — answer only
Use **ask** when the user is primarily asking a question, requesting an explanation, comparing options, reviewing an idea, or asking what something does.

Examples:
- "Why is this happening?"
- "What does this file do?"
- "Should I use FastAPI or Flask here?"
- "Explain this error"
- "How would this architecture work?"

Rules:
- Do **not** modify files.
- Do **not** run build, test, install, or migration commands unless the user explicitly asks.
- Do **not** produce code changes just because a fix seems obvious.
- Keep the reply focused on explanation, tradeoffs, diagnosis, or examples.
- If implementation would help, end by recommending **plan** or **agent** instead of taking action automatically.

### 2. Plan mode — investigate and design, but do not implement
Use **plan** when the user wants a roadmap, implementation approach, task breakdown, architecture proposal, refactor strategy, or file-by-file change plan.

Examples:
- "How would you implement this?"
- "Give me a plan"
- "Break this into steps"
- "What files would need to change?"
- "Design the backend and frontend flow"

Rules:
- You may inspect relevant files and documentation.
- You may produce checklists, phased plans, risk analysis, and testing strategy.
- Do **not** edit files.
- Do **not** run destructive or modifying commands.
- End with a concrete implementation plan that can be executed later in **agent** mode.

### 3. Agent mode — implement, validate, and summarize
Use **agent** only when the user explicitly asks to build, change, fix, refactor, create, wire up, or validate code.

Examples:
- "Implement this"
- "Fix the bug"
- "Create the parser"
- "Add the scoring endpoint"
- "Refactor this module"

Rules:
- Inspect the relevant code before changing it.
- Make the smallest change set that satisfies the request.
- Prefer readable, well-named, maintainable code over clever code.
- Run relevant validation after changes when practical.
- Summarize what changed, how it was validated, and any follow-up work.

## Mandatory intent guardrails

Before taking action, classify the request using this order:
1. **Is the user asking a question only?** → use **ask**
2. **Is the user asking for a strategy or breakdown?** → use **plan**
3. **Is the user explicitly asking for edits or execution?** → use **agent**

If the request mixes modes, follow the **highest-confidence explicit intent**.
- "Explain the issue and fix it" → **agent**
- "Explain the issue, don’t change anything" → **ask**
- "Review this and tell me how you’d fix it" → **plan**

If the user says things like **"just explain"**, **"don’t change code"**, **"question only"**, or **"no implementation"**, that instruction overrides everything else.

## Context usage rules

- Read only the files that are relevant to the request.
- Use repository docs as source of truth before inventing assumptions.
- Prefer existing project terminology over generic AI wording.
- If information is missing, say what is unknown instead of guessing.
- Respect the current maturity of the codebase; don’t design as if unfinished modules already exist.

## Code quality rules for this repo

This project is a resume-tailoring web app with a **FastAPI backend** and **Vue 3 frontend**.
The docs in `high level idea.md` and `project-scope.md` are the product source of truth.

### Backend expectations
- Keep HTTP handlers thin and move business logic into focused modules.
- Favor deterministic logic for ATS scoring and parsing where possible.
- Keep LLM-specific behavior isolated from deterministic scoring logic.
- Use clear data contracts and explicit return shapes.
- Prefer small pure functions for parsing, scoring, and transformations.

### Frontend expectations
- Keep components focused and easy to scan.
- Prefer typed state and explicit props/events.
- Build minimal UI first; add polish without obscuring structure.
- Keep async/loading/error states explicit.

### Resume-domain constraints
- Suggestions must preserve the user’s original voice.
- Do not fabricate metrics, achievements, or facts.
- Restrict rewrite suggestions to **Work Experience**, **Projects**, and **Technical Skills** unless the user explicitly expands the scope.
- Favor minimal edits over full rewrites.

## Performance and readability rules

- Prefer simple architecture over framework-heavy solutions unless complexity clearly requires it.
- Avoid unnecessary abstractions, deep inheritance, and speculative generalization.
- Optimize hot paths and repeated work, but do not sacrifice clarity for premature micro-optimizations.
- Use descriptive names, straightforward control flow, and cohesive modules.

## Output expectations

### In ask mode
Return the answer directly, with optional examples or next-step suggestions. No edits.

### In plan mode
Return:
- a concise goal summary
- the proposed steps
- likely files/modules affected
- risks or unknowns
- validation strategy

### In agent mode
Return:
- what changed
- why it changed
- how it was validated
- any remaining follow-ups or caveats

## Final behavior reminder

**Do not implement just because you can.**
If the user asked for understanding, give understanding.
If the user asked for a plan, give a plan.
Only enter implementation mode when the user clearly wants code or execution.
