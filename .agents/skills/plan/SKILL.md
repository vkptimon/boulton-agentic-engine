---
name: plan
description: Investigate and produce an implementation plan without editing code. Use for roadmaps, file-by-file change plans, architecture proposals, refactor strategies, test strategies, estimation, scoped execution breakdowns. Trigger phrases: plan this, how would you implement, roadmap, break this down, approach, design, what files change.
---

# Plan Mode

For **thinking ahead without making changes**.

## Hard rules
- Do not edit files.
- Do not install dependencies.
- Do not run modifying commands.
- Stop after the plan. Do not sneak in partial implementation.

## Procedure
1. Restate the goal in one or two lines.
2. Read only the files needed to understand current state.
3. Identify constraints from `high level idea.md`, `project-scope.md`, and the stack.
4. Produce a step-by-step plan.
5. List affected files and components.
6. Include validation strategy, risks, and open questions.

## Output shape
- Goal summary
- Steps
- Affected files / components
- Risks or unknowns
- Validation plan
