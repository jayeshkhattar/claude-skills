# Story Template (Dev Lead → Dev Agent)

A story is a self-contained brief. The agent receiving it has no conversation history, no memory of the architecture discussion, and no way to ask a follow-up question. Everything it needs is in the story or it isn't there at all.

## Template

```markdown
## STORY-<id>  |  Level <n>/5  |  Tier: <fast | mid | strong>

### Description
<One paragraph: what this unit does and where it sits in the system.>

### Interface contract
<The exact signature/schema this unit must implement, copied from the architecture.
Inputs, outputs, error behavior. This is what other units build against —
it cannot change without going back to the dev lead.>

### Context
<Minimum background: files to read, conventions to follow, relevant design-record
decisions. Paths, not descriptions of paths.>

### Files owned by this story
<Explicit list. This agent writes here and nowhere else.>

### Acceptance criteria
1. <Externally observable and testable.>
2. <...>

### Dev testing criteria
- <Unit test that must exist and pass.>
- <Edge cases required: empty, boundary, malformed, concurrent — whichever apply.>
- Command to run the tests: <exact command>

### Performance target
<Expected input size, and the complexity or latency budget for the core operation.>

### Definition of done
<A single verifiable end state: "tests pass via <command>, and <artifact> exists at
<path> conforming to <schema>.">

### Termination conditions
- Attempt budget: <N> attempts at making tests pass. On exhaustion, stop.
- Scope rule: if the fix requires touching a file not owned by this story, stop.
- Contract rule: if the interface contract appears wrong or unimplementable, stop —
  do not redesign it locally.
- On any stop: write BLOCKED.md at <path> containing what was attempted, what failed,
  what you believe the cause is, and what you'd need to proceed. Then end.

### Reporting
- Deliverables: <exact paths>
- Status file: <path>, format: {story_id, status, tests_passed, notes}
```

## Why the termination conditions matter

An agent without a stopping rule will keep going. The characteristic failure is not a crash — it's an agent that, on attempt six, quietly decides the interface contract was wrong, rewrites it, and produces something that passes its own tests and breaks three other units. The scope rule and contract rule exist specifically to catch that. A clean `BLOCKED.md` is a good outcome: it costs one story and surfaces a real design problem early.

## Worked example

```markdown
## STORY-04  |  Level 1/5  |  Tier: mid

### Description
Price extraction unit. Given raw HTML from a competitor product page, extract the
current price and currency. Sits between the fetcher (STORY-03) and the differ
(STORY-05).

### Interface contract
extract_price(html: str, site_config: SiteConfig) -> PriceResult
PriceResult = {amount: Decimal, currency: str (ISO 4217), confidence: float 0-1,
               extracted_at: datetime}
Raises ExtractionError(reason, html_snippet) when no price is locatable.
Never returns None; never returns a float for amount.

### Context
- Site configs: src/config/sites.yaml (selector strategies per domain)
- Conventions: src/README.md — Decimal for all money, never float
- Design record decision D-07: confidence below 0.6 is treated as a failure upstream

### Files owned by this story
src/extract/price.py, tests/extract/test_price.py

### Acceptance criteria
1. Extracts price from all 6 fixture pages in tests/fixtures/pages/ with confidence >= 0.9
2. Returns correct ISO 4217 currency for USD, EUR, GBP fixtures
3. Raises ExtractionError (not a generic exception) when the selector matches nothing
4. Handles thousands separators and both comma and period decimal conventions

### Dev testing criteria
- One test per fixture page asserting exact Decimal equality
- Malformed input: empty string, non-HTML, HTML with no price node
- Boundary: price of 0, price above 1,000,000, price with 3 decimal places
- Command: pytest tests/extract/ -v

### Performance target
Pages up to 2MB, ~200 extractions per run. Must be O(n) in document size —
no repeated full-tree scans per candidate selector.

### Definition of done
pytest tests/extract/ passes with 0 failures, and src/extract/price.py exports
extract_price matching the contract signature exactly.

### Termination conditions
- Attempt budget: 5 attempts at green tests.
- Scope rule: if a fix requires editing the fetcher or sites.yaml, stop.
- Contract rule: if PriceResult can't represent a real case (e.g. price ranges),
  stop — do not extend the schema locally.
- On stop: write work/STORY-04/BLOCKED.md with attempts, failure, cause, needs.

### Reporting
- Deliverables: src/extract/price.py, tests/extract/test_price.py
- Status file: work/STORY-04/status.json
```
