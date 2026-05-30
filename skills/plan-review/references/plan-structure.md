# Plan Structure

This document defines the universal plan structure used by the plan-review skill.
The main agent must produce this structure from either an explicit plan document or conversational context.

## Structure

```yaml
Goal:
  One sentence describing what this plan aims to achieve.

Background:
  Why this work is needed — the trigger, the problem, or the motivation behind it.

Scope:
  Included:
    - What is within scope for this plan.
  Excluded:
    - What is explicitly out of scope (anti-scope).

Steps:
  - Step: "Step 1: Short description of the action"
    CompletionCondition: "Specific, verifiable criteria that indicate this step is done."
  - Step: "Step 2: Short description of the next action"
    CompletionCondition: "..."

Constraints:
  - Time, budget, technology, or environment limitations.

KnownRisks:
  - Any risks or assumptions that are already known at planning time.
```

## Field Rules

| Field | Required | Notes |
|-------|----------|-------|
| `Goal` | Yes | Must be a single sentence. If hard to express in one sentence, the plan may be too broad. |
| `Background` | Recommended | Helps the reviewer understand motivation. Omit only if the goal is self-explanatory. |
| `Scope` | Yes | `Included` must have at least one item. `Excluded` may be empty. |
| `Steps` | Yes | Must have at least 2 steps. Fewer than 2 → the task is too simple to review (auto-pass). Each step must have a verifiable `CompletionCondition`. |
| `Constraints` | Recommended | Omit if there are truly no constraints (rare). |
| `KnownRisks` | Recommended | Helps distinguish between "already known" and "overlooked". Empty list is valid. |

## Example

```yaml
Goal: Implement a rate-limited retry wrapper for the OpenAI API client.
Background: The current implementation crashes on 429 responses. We need graceful degradation.
Scope:
  Included:
    - Token-bucket rate limiter
    - Exponential backoff with jitter
    - Configurable max retries
  Excluded:
    - Circuit breaker pattern (future work)
    - Client-side caching
Steps:
  - Step: "Step 1: Write failing test for rate-limited retry"
    CompletionCondition: "pytest shows 1 failure for the expected reason"
  - Step: "Step 2: Implement token-bucket rate limiter"
    CompletionCondition: "Rate limiter unit tests pass"
  - Step: "Step 3: Integrate retry logic into API client"
    CompletionCondition: "Integration test with mocked 429 responses passes"
Constraints:
  - Node 20+
  - No new external dependencies
  - Budget: under $0.50 in API test costs
KnownRisks:
  - OpenAI API rate limits vary by tier; our limit may be lower than expected
  - Concurrent request ordering may affect token bucket accuracy
```
