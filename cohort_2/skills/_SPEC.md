# SKILL.md spec

Canonical structure for every skill in this workspace. New skills (scaffolded via `/build-it` or by hand) should follow this shape. Two exceptions, called out at the bottom.

---

## Frontmatter

```
---
name: skill-name
description: One-sentence description. Include when to use the skill, such as "Use when the user asks for X, Y, or Z."
---
```

Use lowercase letters, digits, and hyphens only for `name`, and name the skill folder exactly after the skill name. Codex uses `name` and `description` as the load-bearing metadata for discovery; tool access, model selection, schedules, and heartbeat wiring belong to the harness configuration, not SKILL.md frontmatter.

---

## My setup

User-customized fields, filled in at install time. Use `<...>` placeholders that the install flow replaces. Example:

```
## My setup

### Format
<default value or placeholder for user's choice>

### Hard cap (words)
<default cap; the user can override>
```

If any placeholder is still present at runtime, the skill body should stop and ask the user to seed it before running.

---

## # Skill Name

One paragraph: what the skill does, when it fires, what the user gets back. Keep to 3-4 sentences.

---

## Inputs

Bullet list of what the skill reads at the top of every run:

- The "My setup" section above
- Upstream data sources (calendar via Composio, inbox via Composio, web search, etc.)
- `skills/<skill-name>/feedback.md` — recent reactions; honor them
- Optional: USER.md sections the skill uses (Focus, Patterns, Style, etc.)

---

## Process

5 to 6 bullet points. Each bullet describes one step in plain English; the agent fills in details (which tool action, which filter, etc.). Don't write paragraph-form sub-steps or branch trees unless the branching is intrinsic.

End every user-facing output with the feedback invitation:

> Anything you want me to change for next time? What worked, what didn't?

When the user replies with feedback, append a dated free-form note to `skills/<skill-name>/feedback.md` and confirm:

> Got it. I'll remember this for next time.

(The detailed convention for processed feedback — `[FIXED ...]` / `[SKIPPED ...]` inline tagging — lives in `AGENTS.md` Feedback Loop and in `skill-review/SKILL.md`. Skills don't need to repeat it.)

---

## Hard rules

Short bullets. Each rule starts with "Never" or "Always" so it reads as an invariant. Examples:

- Never invent facts not present in the source.
- Never exceed the word cap from "My setup."
- Ask the user before sending anything that touches another person's surface (email, message, post). Save drafts; send only on explicit approval.

---

## When the skill fails

What to log, what to send the user. Typical pattern:

- Log to `skills/<skill-name>/failures/YYYY-MM-DD.md` with the reason.
- Send the user one short Telegram note explaining what failed. Don't retry repeatedly.

---

## Trust ceiling and graduation (only if applicable)

Skills that touch external surfaces (sending email, posting, paying) need a trust-tier story. Document:

- The current trust ceiling (typically AUGMENT for new skills: drafts only, ask before sending)
- How to graduate to AUTOMATE after `skill-review` validates the pattern (typically: change one hard rule from "ask before sending" to "send after drafting")

Skills with no external surface (read-only fetches, summarization, indexing) don't need this section.

---

## What's OpenClaw native vs workshop convention

Short reference table:

```
| Mechanism | Source |
|---|---|
| <thing> | OpenClaw native / Composio / Workshop addition / etc. |
```

Helps the user understand which patterns transfer to other harnesses and which are workshop-specific.

---

## Two exceptions to this spec

- **`skill-review/SKILL.md`** is a meta-skill — it operates on other skills' feedback files. Its structure differs (Feedback tagging convention section, per-skill review loop). Diverges from this spec by design.
- **`build-it/SKILL.md`** is a guided conversational flow with 11 steps for scaffolding new skills. Its structure differs because it IS the new-skill scaffolder. Diverges from this spec by design.

Every other skill should follow the spec above. New skills `/build-it` scaffolds will follow it by default.
