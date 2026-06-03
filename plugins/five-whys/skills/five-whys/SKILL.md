---
name: five-whys
description: Facilitate a Five Whys root cause analysis for a problem
argument-hint: <problem description>
---

Facilitate a Five Whys root cause analysis for the problem the user has described.

The Five Whys is an iterative interrogative technique for tracing symptoms back to their root cause by repeatedly asking "Why?" — each answer becoming the basis for the next question.

## Process

The user's problem is: $ARGUMENTS

If no problem was provided, ask the user to describe the problem before proceeding.

Work through the analysis interactively with the user:

1. **State the problem** clearly. Ask the user for confirmation using a structured question with two options — "Yes, that's right" and "No, let me clarify" — before proceeding.

2. **Ask "Why?" sequentially** — up to five times, or until you reach a root cause. After each answer:
   - Probe deeper if the answer is still a symptom (something that happened) rather than a cause (a system failure, missing process, or structural gap).
   - Stop early if you've clearly reached a root cause before five iterations.
   - Branch into multiple chains if the answer reveals more than one contributing cause.

3. **Identify the root cause(s)** — the underlying system failure or gap that, if fixed, would prevent the problem from recurring.

4. **Propose corrective actions** targeted at the root cause, not the symptom. Frame each action as: what to change, why it addresses the root cause, and how to verify it worked.

## Presentation

Present the analysis as a numbered chain:

- Why 1: [symptom] → [first cause]
- Why 2: [first cause] → [deeper cause]
- ...and so on

Conclude with:
- **Root cause:** [concise statement]
- **Recommended action(s):** [specific, actionable fixes]

## Important caveats to keep in mind

- Five is a guideline, not a rule. Stop when you reach a systemic failure, not when you hit the count.
- If multiple causes branch off at any step, pursue the most significant branch first, then note the others.
- Avoid stopping at human error as the root cause — ask why the error was possible (missing training, unclear process, inadequate tooling).
- If the user's answers are vague, ask clarifying questions rather than guessing.
