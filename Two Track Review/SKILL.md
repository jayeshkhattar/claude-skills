---
name: two-track-review
description: Answer review and audit requests in two stacked versions — a full technical write-up, then a short plain-language recap. Use this skill whenever the user asks you to review, audit, critique, assess, or do a post-mortem on something (code, a PR, an architecture, a doc, a plan, a config, a design), or asks for a substantive walkthrough of how a non-trivial system works. Trigger it on phrases like "review this", "take a look at", "what do you think of this code", "audit this", "critique this design", "walk me through how this works". Use it even when the user does not explicitly ask for two versions — the two-version format is the default for these requests. Do NOT use it for routine conversational explanation ("what did you just change", "why did that fail", "explain this one line"), narrow factual questions, or any answer that would be under ~150 words on its own.
---

# Two-Track Review

Reviews get read twice by the same person, for different reasons. Once carefully, to work through the details or hand to another agent. Once quickly, days later, to remember what the conclusion was. One document rarely serves both. So produce both, in one response, in a fixed order.

## When this applies

Applies to: code review, PR review, architecture review, design critique, document review, post-mortems, debugging write-ups, comparisons of approaches, and walkthroughs of systems complex enough to have real findings.

Does not apply to: routine conversational explanation of work just done, narrow factual questions ("what's the syntax for X"), writing or editing code, or anything where the user asked for a single artifact.

The gate is **findings**, not topic. A finding is something the reader could act on or be wrong about — a defect, a risk, a tradeoff, a non-obvious mechanism. If the honest answer has fewer than three findings, or comes in under ~150 words, answer directly with no headers. Splitting a short answer into two tracks is noise, and forcing the structure onto a one-line answer is worse than not using this skill at all.

If a dedicated structured-findings tool is available and appropriate for the request, use it — it replaces Track 1. Still write Track 2 underneath it.

## Output format

Use this exact structure and these exact headers:

```
## Detailed

[full technical version]

---

## The 2-minute version

[plain-language version]
```

No preamble before `## Detailed`. Nothing after the short version — no offers to expand, no follow-up questions, no "let me know if you want me to dig into X". The user drives what comes next.

## Track 1 — Detailed

Written for a technically fluent reader or for another AI agent to consume as input.

Length is set by the number of findings, not by a target. A 20-line diff with one real problem gets a few paragraphs. A subsystem audit gets pages. Padding to feel thorough is a defect — if there are three findings, write three findings and stop.

- Lead with the verdict or headline finding, then support it. Never build up to it.
- Be specific: name files, functions, line numbers, method names, table names, config keys. "The validation is weak" is useless; "`validateSigner()` trusts `req.body.role` without checking it against the session" is a finding.
- Separate what is broken from what is merely a preference. Label severity explicitly (blocking / should-fix / nit) so the reader can triage.
- Show the reasoning chain for anything non-obvious — why it fails, under what conditions, what the blast radius is.
- Include the fix or the concrete next action for each finding. A review that lists problems without directions is half-finished.
- Use jargon freely here. This track assumes expertise.

**When there are many findings** (roughly eight or more, or more than three files), group them — by file, by subsystem, or by severity, whichever cuts cleanest — and put a short ordered list of the blockers at the top before the groups. Ungrouped long lists are unreadable and the reader loses the priority signal.

## Track 2 — The 2-minute version

Written for the same person on a different day, in a hurry, with no context loaded. Two to three minutes, regardless of how long Track 1 ran. This is not a teaser — it must stand alone.

- Bullets, not paragraphs. Each bullet is one idea, one or two lines.
- Plain language. Replace jargon with what the jargon *does*. Not "the mutex is contended" but "two things try to write at the same time and one waits."
- Stay concrete. Keep the file names, numbers, and real consequences. Simplifying the language does not mean going vague — "it's fine but could be cleaner" is a failed bullet.
- Carry the severity signal across. The reader must be able to tell the blocker from the nit without opening Track 1.
- Say what it means for the reader: what breaks, what it costs, what to do first.
- Do not condescend. Short and clear, not slow and cutesy. No "think of it like a highway!" analogies unless the analogy genuinely does work the explanation can't.
- End with a one-line bottom line: ship it / fix these two things first / this approach won't hold.

If the short version and the long version disagree on what matters most, the long version is wrong — fix it.

## Example

For a review of an Apex trigger that does a SOQL query inside a loop:

**Detailed** names the class and method, explains governor limits and the exact row count at which it fails, shows the bulkified rewrite, and separately flags two naming nits as non-blocking.

**The 2-minute version:**

- `AccountTriggerHandler.updateContacts()` queries the database inside a loop — it works in testing and breaks in production
- Fails once a batch has ~200 records; Salesforce cuts it off at 100 queries
- Fix: pull the query out of the loop, collect the IDs first, query once
- Two variable names are unclear — cosmetic, ignore for now
- Bottom line: don't merge until the loop is fixed; everything else is fine

## Failure modes to avoid

- Applying the format to a request that didn't earn it. When in doubt, don't.
- Writing the short version first and inflating it into the long one. Write the detailed version, then distill.
- Making the short version a table of contents ("covers three issues in the auth layer") instead of the actual answer.
- Dropping the severity signal in the short version.
- Appending questions or offers after the short version.
