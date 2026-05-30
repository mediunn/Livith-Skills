# Review Dimensions

This document defines the six dimensions the review subagent evaluates
against the plan structure and implementation output. Each dimension includes
what to look for, the severity classification, and example findings.

## Golden Rule: Read the Actual Output, Not the Report

The agent who produced the output claims it is complete. **Do not trust the
report. Read the code/files yourself.** Most missed issues come from taking
the implementer's word instead of verifying against the actual artifact.

## 1. Spec Compliance

Does the output cover everything required by the Plan?

**What to check:**
- Does every requirement in the Plan's Goal, Scope, and Steps have a corresponding
  implementation in the output?
- Are there any requirements explicitly listed in Scope/Included that are missing?
- Are CompletionConditions from each Step actually met?
- For multi-file outputs: does each file serve a requirement, or is it superfluous?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A core requirement from Goal or Scope is completely missing | "Goal says 'JWT auth' but no token validation exists anywhere" |
| 🟡 Major | A non-critical requirement is missing | "Scope included 'email confirmation', not implemented" |
| 🟢 Minor | A nice-to-have is missing | "CompletionCondition says 'add logging', missing" |

## 2. Simplicity / YAGNI

Is the output the minimum work that satisfies the requirements? Nothing speculative.

**What to check:**
- 🚨 **Features beyond scope**: Does the output include anything NOT in the Plan?
- 🚨 **Over-abstraction**: Are there interfaces, factories, or base classes that have
  only one implementation?
- 🚨 **Premature configurability**: Are there config flags, env vars, or parameters
  for values that never need to change?
- 🚨 **Error handling for impossible scenarios**: Defensive code for conditions that
  cannot occur given the constraints.
- 🚨 **Unused code**: Functions, variables, imports, or types that serve no purpose.
- 🚨 **Speculative flexibility**: Comments like "in case we need it later" or
  patterns designed for hypothetical future requirements.
- Ask: "Would a senior engineer say this is overcomplicated?"

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | Major violation of the Principle of Minimum Code | "500-line implementation when 50 lines would cover the Plan. Whole module does things never requested." |
| 🟡 Major | Unnecessary abstraction or feature creep | "Abstract factory pattern for a single data source" |
| 🟢 Minor | Small over-engineering | "Unused import left in production code" |

**Calibration:** Be aggressive here. This is the dimension most reviewers
under-weight. If you see *anything* that wasn't asked for, flag it.

## 3. Correctness

Is the output logically correct? Are there bugs or errors?

**What to check:**
- Does the code/design do what it claims to do?
- Are there obvious logic errors (infinite loops, off-by-one, null dereference)?
- For code: does it handle normal, edge, and error cases appropriately?
- For documents: are there factual errors or contradictions?
- Do function signatures match their usage?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A bug that would cause data loss, crash, or security hole | "SQL injection vulnerability in dynamic query building" |
| 🟡 Major | A bug that would cause incorrect behavior | "Off-by-one in pagination skips first result" |
| 🟢 Minor | A minor logic issue unlikely to manifest | "Comparison uses `==` instead of `===` (JS, no type coercion issue)" |

## 4. Consistency

Does the output follow established patterns, naming, and structure?

**What to check:**
- Does the output follow the codebase/project's existing naming conventions?
- Are there inconsistent patterns within the output itself?
- For code: does it follow the language/framework idioms?
- For documents: does the formatting, tone, and structure match existing docs?
- Are there mix-and-match styles (e.g., tabs and spaces, camelCase and snake_case)?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | Inconsistency that breaks the build or causes confusion | "New file uses ES modules while entire codebase is CommonJS" |
| 🟡 Major | Consistent deviation from project standards | "snake_case for functions when project uses camelCase everywhere" |
| 🟢 Minor | Minor inconsistency | "Mixed quotation style within one file" |

## 5. Quality

Is the output well-built? Security, performance, maintainability, and robustness.

**What to check:**
- **Security**: Hardcoded secrets, missing input validation, permission issues?
- **Performance**: N+1 queries, unnecessary allocations, blocking calls?
- **Maintainability**: Clear naming, reasonable comments, testability?
- **Robustness**: Error handling, logging, retry/fallback where appropriate?
- **Test coverage**: Are there tests for the implementation? Do they actually
  test the right things?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A security vulnerability or performance disaster | "API key hardcoded in source file" |
| 🟡 Major | Significant quality concern | "No error handling on external API calls" |
| 🟢 Minor | Minor quality nit | "Function lacks docstring" |

## 6. Fresh Perspective

Read the output cold, as if seeing it for the first time. What breaks first?
What assumptions are baked in? This dimension catches what the other five
miss — the "oh no" moments that happen in production, not in review.

**What to check:**
- Does the output actually do what the function/variable names suggest?
- What breaks first when this hits production?
- What assumptions are baked in that aren't guaranteed (network availability,
  file existence, timing, concurrency)?
- What happens with unexpected input, concurrent access, or partial failure?
- What will the person maintaining this in 6 months curse about?
- Is there a simpler way to achieve the same thing?

**Severity guidelines:**
| Level | Condition | Example |
|-------|-----------|---------|
| 🔴 Critical | A hidden assumption that WILL break in production | "Assumes network call always succeeds, no retry or fallback" |
| 🟡 Major | A plausible production risk | "File path is hardcoded — works in dev, breaks when deployed" |
| 🟢 Minor | A concern worth noting | "Function has 3 parameters with similar types; caller may mix them up" |
