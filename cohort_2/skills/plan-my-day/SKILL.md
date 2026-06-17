---
name: plan-my-day
description: Generate a structured plan for the user's day from their Google Calendar (via Composio), with focus blocks for this-week priorities and working hours. Ask the user inline for priorities and hours if they're not in USER.md. Output time-blocked plan sent to Telegram. Invoke when user says "plan my day", "what's my day look like", "plan today", or via the morning cron.
---

## My setup

This section gets customized at install time. If placeholder text (`<...>`) is still present, ask the user to seed it before running.

### Format
<default: schedule + focus blocks + heads-up + first move. The user can pick a flat list or a paragraph instead.>

### Hard cap (words)
<default: 350.>

---

# Plan My Day

Generate the user's daily plan from their Google Calendar via Composio. Add focus blocks for this-week priorities within their working hours. Output goes to Telegram.

## Inputs

- The "My setup" section above (format and word cap)
- Today's calendar via Composio Google Calendar
- The user's working hours and this-week priorities (ask them if not already known)
- `skills/plan-my-day/feedback.md` (recent reactions; honor them)

## Process

1. Fetch today's calendar via Composio. If the fetch fails, tell the user the connection isn't reachable and stop.
2. Get working hours and this-week priorities. Try USER.md first (Focus and Patterns sections, if present). If absent or empty, ask the user inline. If they say "skip" on priorities, omit the focus-blocks section entirely.
3. Find focus blocks in calendar gaps ≥ 60 minutes within working hours. Don't propose blocks under 30 minutes. Reserve them for the priorities the user gave.
4. Format the plan in their chosen shape, capped at the word limit from "My setup."
5. Send via Telegram.
6. When the user replies with feedback, append a dated free-form note to `feedback.md` and confirm: "Got it. I'll remember this for next time."

End every plan with: *Anything you want me to change for next time? What worked, what didn't?*

## Hard rules

- Never invent events not in the calendar.
- Never modify or delete calendar events. Read-only.
- Never propose a focus block during an existing event.
- Never propose focus blocks outside the user's working hours.
- Never exceed the word cap from "My setup."
- Never send if today's calendar pull failed. Tell the user the reason.

## When the plan fails

If Composio Calendar is unreachable, log to `skills/plan-my-day/failures/YYYY-MM-DD.md` and send a short note: "Plan failed: <reason>. Will retry tomorrow."

If today has zero events and zero priorities, send a single-line plan: "🌅 Today is wide open. No meetings, no priorities flagged. Use it well."

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| Composio Google Calendar toolkit | Composio |
| `USER.md` Focus + Patterns sections | OpenClaw native (5-file identity model) |
| Inline "My setup" in SKILL.md | OpenClaw native pattern |
| `skills/plan-my-day/feedback.md` | Workshop addition |
| `skills/plan-my-day/failures/` | Workshop addition |
