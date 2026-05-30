# Review Dimensions

This document defines the five dimensions the review subagent evaluates
against the plan structure. Each dimension includes what to look for,
the severity classification, and example findings.

## 1. Completeness

Does the plan cover everything needed to achieve the goal?

**What to check:**
- Does every requirement implicit in the Goal and Scope appear as a step?
- Are there external dependencies or prerequisites not accounted for?
- Is there a gap between "what needs to happen" and "what the steps describe"?
- For document-based plans: do all spec requirements map to at least one step?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A missing step would block the entire goal | "Database migration is required but no step prepares the schema" |
| 🟡 Major | A missing step creates significant rework or risk | "Error handling for API calls is not addressed anywhere" |
| 🟢 Minor | A nice-to-have is missing | "Logging is not mentioned but could be added later" |

## 2. Clarity

Are the steps, scope, and success criteria specific enough to execute without ambiguity?

**What to check:**
- Does any step contain vague phrasing like "handle appropriately", "deal with", "process as needed"?
- Are completion conditions verifiable? ("tests pass" is good; "works correctly" is not)
- Is the Scope/Excluded boundary clear? Could a reader misunderstand what's included?
- Is the Goal specific enough to know when it's achieved?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A step has no verifiable completion condition | Step: "Improve performance" — no metric, no target |
| 🟡 Major | Ambiguity that will likely lead to wrong implementation | "Handle errors appropriately" — no error strategy defined |
| 🟢 Minor | Minor ambiguity that context will resolve | "Update the config" — doesn't specify which config file, but there's only one |

## 3. Feasibility

Is the plan realistic given constraints, time, and available resources?

**What to check:**
- Are the steps achievable within stated constraints (time, budget, technology)?
- Are there hidden resource requirements (permissions, API keys, hardware)?
- Does any step depend on something that doesn't exist yet?
- Is the plan scope proportional to the implied effort?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A step is impossible under given constraints | "Deploy to Kubernetes" when constraint says "no k8s cluster available" |
| 🟡 Major | A step is impractical or significantly under-resourced | "Train ML model in 30 minutes" with no mention of GPU or dataset prep time |
| 🟢 Minor | A stretch but achievable | "Optimize to under 100ms" — aggressive but possible with profiling |

## 4. Risks & Assumptions

What hidden assumptions does the plan make? What could go wrong that isn't planned for?

**What to check:**
- Does the plan assume external systems behave perfectly (no rate limits, no downtime)?
- Are there implicit assumptions about the user's environment (OS, installed tools, network)?
- Does the plan assume a specific approach without considering failure modes?
- Are the KnownRisks in the plan structure actually addressed? (Known but ignored ≠ addressed)

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | An unstated assumption would invalidate the entire plan if wrong | "Assume 100% uptime of external API" with no fallback |
| 🟡 Major | A plausible risk with no mitigation | "Third-party service may change API" but no version pinning or contract test |
| 🟢 Minor | A low-probability risk worth noting | "Developer may be unfamiliar with the ORM" — mention for awareness |

## 5. Alternatives

Has the chosen approach been justified? Could a simpler or more robust approach work better?

**What to check:**
- Is the first reasonable approach accepted without considering trade-offs?
- Is there an obviously simpler solution that would achieve the same goal?
- Does the plan over-engineer for the stated scope?
- Is there an existing solution or pattern in the codebase being ignored?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | The chosen approach is clearly wrong for the goal | "Rewrite the entire module" when a single function fix would suffice |
| 🟡 Major | A clearly better alternative exists | "Build custom auth" when the codebase already has an auth framework |
| 🟢 Minor | An alternative worth noting but not blocking | "Consider using `Promise.all` instead of sequential await" |
