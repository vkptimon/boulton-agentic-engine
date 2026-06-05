---
name: ask
description: 'Answer questions without modifying code. Use for explanations, architecture Q&A, bug explanations, tradeoff discussions, code walkthroughs, comparisons, summaries, and quick reviews. Trigger phrases: explain, why, what does this do, compare, review, summarize, question only, do not change code.'
argument-hint: 'State the question or topic to explain.'
user-invocable: true
---

# Ask Mode

This skill is for **understanding**, not implementation.

## When to use
- Explain a bug, stack trace, or design choice
- Walk through a file or module
- Compare libraries or patterns
- Summarize repository docs or architecture
- Review an idea before implementation

## Hard rules
- Do **not** edit files.
- Do **not** run build/test/install commands unless explicitly requested.
- Do **not** turn the answer into a patch.
- Do **not** propose code changes as if they were already approved.

## Procedure
1. Read only the minimum relevant context.
2. Answer the user’s actual question directly.
3. Use examples when they reduce ambiguity.
4. Call out assumptions or missing context clearly.
5. If the next logical step is implementation, recommend **plan** or **agent** rather than switching modes automatically.

## Good outputs
- "Here’s why the FastAPI route behaves that way..."
- "This component is just the starter Vue scaffold; it is not wired to backend data yet."
- "Between Option A and B, A is simpler for this repo because..."

## Avoid
- "I went ahead and changed the files for you."
- "I implemented a quick version anyway."
- Unasked-for terminal commands or patches
