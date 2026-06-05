---
name: plan
description: 'Investigate and produce an implementation plan without editing code. Use for roadmaps, file-by-file change plans, architecture proposals, refactor strategies, test strategies, estimation, and scoped execution breakdowns. Trigger phrases: plan this, how would you implement, roadmap, break this down, approach, design, what files change.'
argument-hint: 'Describe what needs a plan or design.'
user-invocable: true
---

# Plan Mode

This skill is for **thinking ahead without making changes**.

## When to use
- Create a feature implementation roadmap
- Design backend/frontend boundaries
- Break a large task into milestones
- Estimate complexity or sequencing
- Identify impacted files, tests, and risks

## Hard rules
- Do **not** edit files.
- Do **not** install dependencies.
- Do **not** run modifying commands.
- Do **not** sneak in partial implementation.

## Procedure
1. Restate the goal in one or two lines.
2. Inspect only the files needed to understand the current state.
3. Identify constraints from product docs, architecture, and stack.
4. Produce a step-by-step plan.
5. Call out likely files/modules to change.
6. Include validation strategy, risks, and open questions.
7. Stop after the plan unless the user explicitly asks to continue in **agent** mode.

## Expected output shape
- Goal summary
- Proposed steps
- Affected files/components
- Risks or unknowns
- Validation or test plan

## Good outputs
- "Implement the parser first, then deterministic scoring, then the LLM booster."
- "Split backend logic into parser, scorer, booster, and compiler modules."
- "Update `frontend/src/App.vue` only after the API contracts are defined."
