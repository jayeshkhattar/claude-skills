# QA Test Plan, Bug Reports, and Traceability

QA derives everything here from the **architecture and acceptance criteria**, never from the implementation. Tests written after reading the code inherit the code's assumptions and will pass on bugs the author didn't think of. Write the plan during Phase 5, in parallel with development, and don't revise it to match what got built.

## Test case format

```markdown
### TC-<id> — <short title>
- **Validates:** STORY-<id> / AC-<n>
- **Type:** happy path | boundary | error handling | permission | integration | regression
- **Preconditions:** <system state, fixtures, actor identity>
- **Steps:**
  1. <action>
  2. <action>
- **Expected result:** <precise and observable — a value, a status code, a state change,
  a specific error type. "Works correctly" is not an expected result.>
- **Severity if failed:** blocker | major | minor | cosmetic
```

## Coverage the plan must include

- **Happy path** for every acceptance criterion.
- **Boundaries** — empty, one, many, maximum stated in the Phase 1 input spec, and one past it.
- **Error handling** — every failure path drawn in the Phase 4 architecture. If a failure path has no test, either the test is missing or the path is fictional.
- **Permissions** — one case per actor from Phase 3c, including the adversarial actor: confirm each actor can do what they should and cannot do what they shouldn't.
- **Integration** — every seam between components, exercised with real (not mocked) neighbours at least once.
- **Idempotency and retries** where the architecture claims them.

## Traceability matrix

Build this before execution. Every acceptance criterion maps to at least one test case; every test case maps back to exactly one criterion or an explicit architectural failure path.

| Story | AC | Test cases | Status |
|---|---|---|---|
| STORY-04 | AC-1 | TC-11, TC-12 | pass |
| STORY-04 | AC-3 | TC-14 | fail → BUG-02 |

An acceptance criterion with no test case is a **requirements gap** — report it to the dev lead as a finding in its own right, not as a QA to-do. It usually means the criterion isn't actually testable, which means the story was underspecified.

## Bug report format

```markdown
### BUG-<id> — <one-line symptom>
- **Severity:** blocker | major | minor | cosmetic
- **Found by:** TC-<id>
- **Violates:** STORY-<id> / AC-<n>
- **Environment:** <build/commit, config, data set>
- **Steps to reproduce:** <numbered, minimal, deterministic>
- **Expected:** <...>
- **Actual:** <...>  (attach output/logs/stack trace)
- **Suspected component:** <from the architecture — a hint, not a diagnosis>
```

Report the symptom, not the fix. QA proposing the patch collapses the independence that makes the second track worth running — and QA is often wrong about the cause, because it deliberately hasn't read the code.

## Re-verification rule

When a bug comes back fixed, re-run the failing case **and** every case that touches the same component seam. Fixes that break neighbours are the most common source of round-three bugs. If that keeps happening, the interface contracts are wrong and it's an architecture problem, not a coding problem — escalate rather than looping.

## Sanity and UAT pass (Phase 8)

Not a full regression. A short, high-value sweep:

- Smoke: the primary end-to-end flow, once, on a clean environment.
- One representative path per actor.
- The literal Phase 1 statement: feed the stated input, confirm the stated output.
- Any bug fixed in the final round, re-verified once more.

Report: cases run, passed, failed, open bugs by severity, and an explicit go / no-go with the reason.
