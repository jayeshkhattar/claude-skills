# Prior-Art Research Checklist (Phase 3)

The purpose of this phase is to not build something that already exists, and to not recommend something that will rot under the user in six months. Both errors are expensive; the second is worse because it surfaces later.

## Where to look, in order

1. **The problem's canonical name.** Users describe symptoms; the field has a term. "Watch a page and tell me when it changes" is change detection / web monitoring. Search the canonical name first — it's where the mature tools are.
2. **Package registries directly** — PyPI, npm, crates.io, Maven Central. Registry pages carry release dates and dependency counts that blog posts don't.
3. **GitHub** — sort by stars, then check the commit graph, not the star count.
4. **Awesome-lists and comparison pages** for the domain, as a discovery aid only. They go stale fast; treat every entry as a lead to verify, never as a finding.
5. **The expensive incumbent's competitors.** "Alternative to <SaaS>" surfaces the open-source options that market themselves as replacements.

## Evaluate each candidate on

**Maintenance**
- Date of last release, and last commit to the default branch.
- Open-issue rot: are issues from two years ago still unanswered?
- Bus factor: one maintainer or several?
- A widely-used library with no commits in 18 months may be finished rather than abandoned — check whether issues get responses before concluding either way.

**License**
- Permissive (MIT, Apache-2.0, BSD) vs. copyleft (GPL, AGPL) vs. source-available (BSL, SSPL, Elastic).
- AGPL and SSPL have real consequences for a hosted service; BSL often forbids competing commercial use. If the user's use case might collide with the license, say so plainly and note that you're flagging it, not giving legal advice.
- "Free" in the user's request usually means both zero-cost and unencumbered — check both.

**Fit**
- Does it solve the *core* of the problem or just the periphery? A library that handles 90% of the volume but none of the hard part is not a solution.
- What does adopting it force into the architecture — a runtime, a database, a deployment model, a data format?
- How much glue is left? If the glue is bigger than the library, reconsider.

**Maturity and adoption**
- Who uses it in production? Downloads and dependents are better signals than stars.
- Documentation quality and the existence of a migration/upgrade path.
- Breaking-change history: does it churn its API?

**Cost at the user's scale**
- Free tiers have cliffs. Find the cliff and state it: "free to N requests/month, then $X".
- Watch for costs that are not the sticker price: egress, per-seat, per-connector, storage after retention.
- Self-hosting is not free either — count the operational burden honestly.

## How to report

A short comparison, not a link dump. For each serious candidate: what it is, license, last release, why it fits or doesn't. Then a clear recommendation with a named runner-up and the condition under which you'd switch to it ("if you need X, the runner-up becomes the right call").

State confidence honestly. If you couldn't verify a release date or a license, say that instead of asserting it — recommending an abandoned or mislicensed dependency is worse than recommending nothing. Anything that would take a materially different design if wrong goes into the design record as a numbered assumption.

## When nothing fits

"Nothing exists" is almost always false at the component level and true only at the composition level. Say which parts *do* exist — the API, the framework, the protocol, the algorithm, the reference implementation of the hard subproblem — and specify precisely what glue is missing. That list is the input to Phase 4, and it's usually four or five named things rather than a blank page.
