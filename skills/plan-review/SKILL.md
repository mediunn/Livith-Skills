---
name: plan-review
description: Use this skill whenever the user is about to execute a plan and asks you to review it first — whether they have a formal plan document or just described their approach in conversation. The skill structures their plan, dispatches a cold-eyed subagent to find blind spots, ambiguous steps, feasibility risks, hidden assumptions, and simpler alternatives, then blocks execution until Critical and Major issues are resolved. Key triggers: user says "review my plan", "check my approach", "find gaps", "what am I missing", "실행 전에 검토해줘", "계획에 빠진 부분", "내 계획 좀 봐줘", or anything involving plan validation before starting work. Does NOT trigger for code review, document proofreading, debugging, or writing a new plan from scratch.
---

# Plan Review Skill

Review an execution plan before running it. This skill follows a four-step
workflow: extract a structured plan, dispatch a review subagent, iterate on
issues, and approve.

## Step 1: Extract Plan Structure

Examine the current context — an explicit plan document, a spec, or conversational
context — and produce a structured plan conforming to `references/plan-structure.md`.

**Rules:**
- If a plan document or spec file exists, read it and transform it into the structure.
- If only conversational context exists, extract the plan from what has been discussed.
- The structure must include all required fields from `references/plan-structure.md`.
- If the context is too vague to produce a valid structure, inform the user:
  *"The available context is insufficient to extract a plan. Please clarify the goal
  and scope first."* and stop.

### Plan Structure Reference

```yaml
Goal: One sentence describing the objective.
Background: Motivation and trigger (recommended, not required).
Scope:
  Included:
    - What is in scope.
  Excluded:
    - What is out of scope.
Steps:
  - Step: "Step 1: ..."
    CompletionCondition: "Verifiable criteria."
  - Step: "Step 2: ..."
    CompletionCondition: "Verifiable criteria."
Constraints:
  - Time, budget, tech, or environment limits.
KnownRisks:
  - Pre-identified risks or assumptions.
```

See `references/plan-structure.md` for full field rules and examples.

**Validation:**
- If `Steps` has fewer than 2 items, auto-pass: *"This task is too simple to require
  a review."* and skip to Step 4.
- If the user rejects the extracted plan, ask them to clarify what is wrong and
  adjust before proceeding.

## Step 2: Dispatch Review Subagent (Fork)

Spawn a fork subagent with the extracted plan structure and the reviewer prompt.

**What to pass to the fork:**
```
1. The Plan structure (as YAML or formatted text)
2. The contents of references/reviewer-prompt.md as the instruction
```

**Do NOT pass:**
- The original plan document or full conversation history.
- Your own session context or reasoning process.

The fork will review the plan against these five dimensions
(fully defined in `references/review-dimensions.md`):

| # | Dimension | What It Checks |
|---|-----------|----------------|
| 1 | Completeness | Are there missing steps or gaps? |
| 2 | Clarity | Are steps and criteria specific and verifiable? |
| 3 | Feasibility | Is the plan realistic given constraints? |
| 4 | Risks & Assumptions | What hidden risks or shaky assumptions exist? |
| 5 | Alternatives | Could a different approach work better? |

The fork returns a structured review with status (PASS or BLOCK),
per-dimension summary, and detailed findings severity-graded as:
- 🔴 Critical
- 🟡 Major
- 🟢 Minor

## Step 3: Handle Review Results

Receive the fork's output and check the status.

**If PASS:**
- Proceed to Step 4.

**If BLOCK:**
1. Present the Critical and Major findings to the user.
2. For each finding, propose a concrete fix or revision to the plan.
3. Update the Plan structure accordingly.
4. Re-dispatch the review subagent (Step 2) with the revised plan.
5. Repeat until PASS or the retry limit is reached.

**Retry limit:**
- Maximum 3 review iterations per plan.
- If BLOCK persists after 3 iterations, output:
  *"The plan has been revised 3 times but still has unresolved Critical or Major issues.
  This may indicate the plan itself is fundamentally flawed. Consider rewriting the
  plan from scratch."*
- The user may override the retry limit and proceed with a BLOCK plan if they choose.

**If the fork returns errors or cannot parse the plan:**
- Verify the plan structure is valid per `references/plan-structure.md`.
- Re-dispatch once. If the error persists, fall back to manual inspection.

## Step 4: Approve

Mark the plan as reviewed and approved. Output the final summary and the plan.

**Output format:**

```markdown
## Plan Review Complete ✅

**Status:** PASS (no blocking issues) or PASS (Minor findings only — see notes)

**Final Plan:**

Goal: {goal}
Scope: {included}
Steps: {steps summary}
...
```

If there are any remaining Minor findings, list them as notes so the user
is aware during execution.
