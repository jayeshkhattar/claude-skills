# Claude Skills

A collection of custom skills for Claude Code / Claude Agent SDK.

Each skill lives in its own folder as a `SKILL.md` (plus any `references/` it needs), alongside a packaged `.skill` archive of the same content.

## Skills

- [Two Track Review](Two%20Track%20Review/SKILL.md) — answers review, audit, and critique requests in two stacked versions: a full technical write-up (findings, severity, fixes) followed by a short plain-language recap. Triggers on requests like "review this", "audit this", "critique this design"; stays out of the way for routine conversational explanations or short factual answers.

- [System Architect](System%20Architect%20Skill/SKILL.md) — runs a full architecture and delivery lifecycle on a build request: parse the ask into input/system/output, classify the problem shape, research existing open-source prior art before building, design the architecture, then fork into parallel dev-lead (stories, agents, unit tests) and QA (test plan, validation, bug loop, UAT) tracks. Triggers on "build me X", "design a system that...", "architect this"; scales down for small asks and stays out of single-task requests.
