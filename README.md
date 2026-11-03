# Claude Skills

A collection of custom skills for Claude Code / Claude Agent SDK.

Each skill lives in its own folder as a `SKILL.md` (plus any `references/` it needs), alongside a packaged `.skill` archive of the same content.

## Skills

- [Two Track Review](Two%20Track%20Review/SKILL.md) — answers review, audit, and critique requests in two stacked versions: a full technical write-up (findings, severity, fixes) followed by a short plain-language recap. Triggers on requests like "review this", "audit this", "critique this design"; stays out of the way for routine conversational explanations or short factual answers.

- [System Architect](System%20Architect%20Skill/SKILL.md) — runs a full architecture and delivery lifecycle on a build request: parse the ask into input/system/output, classify the problem shape, research existing open-source prior art before building, design the architecture, then fork into parallel dev-lead (stories, agents, unit tests) and QA (test plan, validation, bug loop, UAT) tracks. Triggers on "build me X", "design a system that...", "architect this"; scales down for small asks and stays out of single-task requests.

## Install

Skills are plain folders, so installing one is a copy. Claude Code looks in two places:

- `~/.claude/skills/` — available in every project
- `<project>/.claude/skills/` — available only in that project (commit it to share with your team)

```bash
git clone https://github.com/jayeshkhattar/claude-skills.git
mkdir -p ~/.claude/skills
cp -r "claude-skills/Two Track Review" ~/.claude/skills/two-track-review
cp -r "claude-skills/System Architect Skill" ~/.claude/skills/system-architect
```

Each target folder must contain `SKILL.md` at its top level. Restart Claude Code (or start a new session) and the skill shows up in the skills list.

The `.skill` files are the same content packaged as an archive, for tools that install skills from a single file (for example, uploading to Claude.ai under Settings → Capabilities → Skills).

## Usage

You don't call a skill by name. Claude reads each skill's `description` and loads it when a request matches:

- "Review this PR" or "audit this config" → Two Track Review. End the message with `1` for the detailed version only, or `2` for the short recap only.
- "Design a system that ingests invoices and flags duplicates" → System Architect.

To force one, ask for it directly ("use the two-track-review skill") or type `/two-track-review` in Claude Code.

## License

MIT. See [LICENSE](LICENSE).
