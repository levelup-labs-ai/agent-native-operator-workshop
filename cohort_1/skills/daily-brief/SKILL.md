---
name: daily-brief
description: Generate the user's daily news brief in their chosen format on the topics they care about. Use when user says "the brief", "today's brief", "what's worth knowing today", "morning brief", or when invoked via the scheduled cron at the configured time.
---

## My setup

This section gets customized at install time. The skill body reads from these fields. If placeholder text (`<...>`) is still present, ask the user to seed it before running.

### Topics I want covered
<topics — three to five things the user wants to read about every day>

### Sources
<default: general web search. The user can narrow to specific blogs, newsletters, or accounts.>

### Format
<default: 3 buckets — must read / worth knowing / skip. The user can pick a short paragraph or a flat 5-bullet list instead.>

### Topics to skip
<topics the user explicitly does NOT want included>

---

# Daily Brief

Produce the user's daily news brief on the topics they care about. The trigger config determines when this fires; you don't need to think about that here.

## Inputs

- The "My setup" section above (topics, sources, format, skip list)
- The last 14 days of daily logs in `memory/YYYY-MM-DD.md` (for cross-day dedup against URLs and headlines you've already covered)
- `skills/daily-brief/feedback.md` (recent reactions; honor them)

## Process

1. Use `web_search` to pull candidate items for each topic. Run one search per topic with today's date in the query (e.g., "AI safety news May 2026") so results are current. Vary phrasings across days; don't rerun identical queries. Do not rely on training knowledge — always search.
2. Filter for novelty against the last 14 days of daily logs. Drop URL repeats and semantic duplicates.
3. Apply recent feedback. If the user said "skip image-gen" or "too long," adapt accordingly.
4. Format in the user's chosen shape, capped at 600 words. Drop bottom-bucket items if you can't fit.
5. Send via Telegram.
6. When the user replies with feedback, append a dated free-form note to `feedback.md` and confirm: "Got it. I'll remember this for next time."

End every brief with a one-line invitation: *Anything you want me to change for next time? What worked, what didn't?*

## Hard rules

- Never include URLs covered in the last 14 days of daily logs.
- Never exceed 600 words total.
- Never include items the user has said to skip in `feedback.md` or in "My setup → Topics to skip."
- Never invent a fact, headline, or link.
- Never send an empty brief. Log to `skills/daily-brief/failures/YYYY-MM-DD.md` and notify briefly instead.

## When the brief fails

Log the reason to `skills/daily-brief/failures/YYYY-MM-DD.md`. Send a short note: "Brief failed today: [one-line reason]. Will retry tomorrow."

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| `skills/<skill-name>/SKILL.md` recipe + inline config | OpenClaw native pattern |
| `memory/YYYY-MM-DD.md` daily logs (read for dedup) | OpenClaw native |
| `skills/daily-brief/feedback.md` | Workshop addition (reviewed on demand with `/skill-review`) |
| `skills/daily-brief/failures/` | Workshop addition |
