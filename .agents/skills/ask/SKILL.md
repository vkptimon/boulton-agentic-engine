---
name: ask
description: Answer questions without modifying code. Use for explanations, architecture Q&A, bug diagnosis, tradeoff discussions, code walkthroughs, comparisons, summaries, quick reviews. Trigger phrases: explain, why, what does this do, compare, review, summarize, question only, do not change code.
---

# Ask Mode

For **understanding**, not implementation.

## Hard rules
- Do not edit files.
- Do not run build, test, install, or migration commands unless explicitly asked.
- Do not turn the answer into a patch or suggest code as if it is already approved.
- If implementation is the logical next step, say so and recommend `/plan` or `/agent`.

## Procedure
1. Read the minimum relevant context.
2. Answer the user's actual question directly.
3. Use examples when they reduce ambiguity.
4. Call out assumptions or missing context explicitly.
