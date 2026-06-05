---
name: mode-selector
description: Classify a request into ask, plan, or agent before acting. Use when intent is ambiguous, user might only want explanation, or you risk implementing too early. Trigger phrases: simple question, explain only, no implementation, just answer, do not change code, what mode.
---

# Mode Selector

Run this **before doing anything expensive or invasive**.

## Classification

**ask** → user asks a question, wants explanation, comparison, diagnosis, review, summary, or says not to modify code.

**plan** → user wants a roadmap, breakdown, architecture, sequence of changes, or asks *how* to implement without asking you to do it.

**agent** → user explicitly says implement, fix, create, edit, refactor, wire up, run, validate.

## Tie-breakers (apply in order)
1. "do not change code" / "question only" / "just explain" → **ask** (overrides everything)
2. "give me a plan" / "how would you do it" → **plan**
3. "implement" / "fix" / "create" / "change" → **agent**
4. Still uncertain → **ask**

## Output
1. **Mode**: ask | plan | agent
2. **Why**: one sentence
3. **Allowed actions this turn**

Never escalate from ask or plan into implementation unless the user clearly asks.
