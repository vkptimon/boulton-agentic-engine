---
name: mode-selector
description: 'Classify a request into ask, plan, or agent before taking action. Use when intent is ambiguous, when the user might only want an explanation, or when you need to avoid implementing code too early. Trigger phrases: simple question, explain only, no implementation, ask mode, plan mode, agent mode, just answer, do not change code.'
argument-hint: 'Describe the user request to classify into ask, plan, or agent.'
user-invocable: true
---

# Mode Selector

Use this skill **before doing anything expensive or invasive**.

The goal is to stop accidental implementation when the user only wants an explanation or a plan.

## Classification rules

### Choose **ask** when
- The user asks a direct question.
- The user wants an explanation, comparison, diagnosis, review, or summary.
- The user says not to modify code.
- The user is exploring options.

### Choose **plan** when
- The user asks for a roadmap, design, breakdown, architecture, or sequence of changes.
- The user wants to know *how* to implement something but has not asked you to do it yet.
- The user wants risks, tradeoffs, or affected files.

### Choose **agent** when
- The user explicitly asks to implement, fix, create, edit, refactor, wire up, run, or validate something.
- The user clearly expects code or file changes in this turn.

## Tie-breakers

When signals are mixed, use these tie-breakers in order:
1. Explicit "do not change code" or "question only" wins -> **ask**
2. Explicit "give me a plan" or "how would you do it" wins -> **plan**
3. Explicit "implement", "fix", "create", or "change" wins -> **agent**
4. If still uncertain, default to **ask**

## Output format

Return three things:
1. **Selected mode**: ask, plan, or agent
2. **Why**: one or two sentences
3. **Allowed actions** for this turn

## Guardrail

Never escalate from ask or plan into implementation unless the user clearly asks for it.
That means:
- no file edits
- no terminal execution
- no package installs
- no hidden implementation work
