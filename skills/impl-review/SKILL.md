---
name: impl-review
description: 'Use this skill after the agent has produced an output — code, document, design, config, or any artifact — to verify it against the original plan or spec. The skill extracts a structured plan from context, identifies the relevant output, and dispatches a cold-eyed subagent to check for spec compliance, simplicity/YAGNI violations, correctness, consistency, and quality. It blocks approval until all Critical and Major issues are resolved. Key triggers: user says "check my implementation", "review the output", "implement 검토", "verify this against the spec", "산출물 리뷰", "구현 검토", or anything about verifying an artifact against requirements. Does NOT trigger for code review unrelated to a specific plan, general proofreading, debugging sessions, or pre-execution plan review (use plan-review for that).'
---

# Implementation Review Skill

Review an output (code, document, design, config, or any artifact) against
the original Plan. This skill is the post-execution complement to plan-review:
after the agent has finished implementing **all steps** of the plan, verify the
final output meets the spec, isn't over-engineered, and is correct, consistent,
and well-made.

**Timing:** This skill runs after the **entire Plan** has been executed, not per
step. The workflow is: `plan → execute (all steps) → review (this skill)`.
If you need per-step review, use plan-review before execution and address
concerns during implementation.

This skill is **independent** of plan-review. It can operate on any plan/spec
regardless of whether plan-review was used beforehand.

## Step 1: Extract Plan Structure

Examine the current context — a plan document, a spec, or conversational context —
and produce a structured plan conforming to `references/plan-structure.md`.

**Rules:**
- If a plan document or spec file exists, read it and transform it into the structure.
- If only conversational context exists (e.g., "I asked you to build a login API"),
  extract the plan from what was discussed.
- The structure must include all required fields from `references/plan-structure.md`.
- If the context is too vague to produce a valid structure, inform the user:
  *"The available context is insufficient to extract a plan. Please clarify the
  goal and scope that this implementation should be measured against."* and stop.

**Validation:**
- If the extracted Plan has no Steps or Goal is missing, ask the user to clarify.
- If the user rejects the extracted plan, adjust based on their feedback.

## Step 2: Identify the Output Under Review

Determine what artifact(s) to review. Collect them from the most appropriate
source in this priority order:

1. **User specifies directly** — "check this file", "review what I just built" →
   read the specified files or re-read the agent's last output.
2. **Plan Scope inference** — The Plan's Steps or Scope/Included mention specific
   files or deliverables (e.g., "Create src/auth/login.ts", "Write docs/api.md").
   Read those files.
3. **Git diff** — If available and a baseline commit can be identified, use
   `git diff` to find changed files, then read their current content.
4. **Conversational context** — The most recent agent output (code block, file path,
   or description of what was done) is the output to review.

Collect the actual content of the output (file contents, code blocks, or
document text) for passing to the reviewer. If the output is too large,
summarize the key parts but include file paths and structure.

If nothing can be identified, inform the user:
*"I could not determine what output to review. Please point me to the artifact
you want checked."*

## Step 3: Dispatch Review Subagent (Fork)

Spawn a fork subagent with the plan structure, the output under review,
and the reviewer prompt.

**What to pass to the fork:**
```
1. The Plan structure (as YAML or formatted text)
2. The output under review (file contents, code, or document text)
3. The contents of references/reviewer-prompt.md as the instruction
```

**Do NOT pass:**
- Your own session context or reasoning process.
- The original conversation history.
- Write/edit permissions of any kind.

**Fork constraints (CRITICAL):**
- The fork is **read-only**. It must NOT write, edit, or create any files.
- The fork's only job is to read the plan and output and return a text review.
- If the fork attempts any file mutations, treat that as a violation.

**CRITICAL: The fork must read the actual output files/code — not trust the
agent's report.** Most missed issues come from accepting claims instead of
verifying against the actual artifact.

The fork will review the output against these six dimensions
(fully defined in `references/review-dimensions.md`):

| # | Dimension | What It Checks |
|---|-----------|----------------|
| 1 | Spec Compliance | Does the output cover everything required by the Plan? |
| 2 | Simplicity / YAGNI | Is this the minimum work that solves the problem? Nothing speculative. |
| 3 | Correctness | Bugs, logic errors, factual mistakes. |
| 4 | Consistency | Follows project naming, patterns, and structure. |
| 5 | Quality | Security, performance, maintainability, robustness. |
| 6 | Fresh Perspective | Read the output cold — what breaks first in production? What hidden assumptions exist? |

The fork returns a structured review with status (PASS or BLOCK),
per-dimension summary, and detailed findings severity-graded as:
- 🔴 Critical
- 🟡 Major
- 🟢 Minor

## Step 4: Handle Review Results

Receive the fork's output and check the status.

**If PASS:**
- Proceed to Step 5.

**If BLOCK:**
1. Present the Critical and Major findings to the user.
2. For each finding, propose a concrete fix or revision.
3. Ask the user if they want to fix the issues or override the BLOCK.
4. If the user chooses to fix, update the output accordingly and loop back
   to Step 2 (re-identify the output) or Step 3 (re-dispatch review).
5. Repeat until PASS or the retry limit is reached.

**Retry limit (adaptive):**
The maximum retry count scales with the Plan's complexity:

| Plan Steps | Max retries |
|------------|-------------|
| 1~5 (small) | 3 |
| 6~15 (medium) | 5 |
| 16+ (large) | 7 |

- If BLOCK persists after the max retries, output:
  *"The output has been revised {N} times but still has unresolved Critical or
  Major issues. Consider whether the Plan itself needs revision."*
- The user may override the retry limit and proceed with a BLOCK if they choose.

**If the fork returns errors or cannot parse the plan/output:**
- Verify the plan structure is valid per `references/plan-structure.md`.
- Verify the output content is readable.
- Re-dispatch once. If the error persists, fall back to manual inspection.

## Step 5: Approve

Mark the implementation review as complete. Output the final summary.

**Output format:**

```markdown
## Implementation Review Complete ✅

**Status:** PASS (no blocking issues) or PASS (Minor findings only — see notes)

**Final Review Summary:**
| Dimension | Status | Key Finding |
|-----------|--------|-------------|
| Spec Compliance | ✅ | All requirements covered |
| Simplicity / YAGNI | ✅ | No over-engineering detected |
| Correctness | ✅ | No bugs found |
| Consistency | ✅ | Style matches project |
| Quality | ✅ | Adequate tests and error handling |

**Minor findings (if any):**
- ...
```
