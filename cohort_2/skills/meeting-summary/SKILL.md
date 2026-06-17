---
name: meeting-summary
description: Produce a clean summary of a meeting (decisions, action items, attendees, open questions) and append it to today's daily log. Source can be Granola via Composio, another connected note-taking toolkit, or content the user pastes directly. Invoke when user says "summarize my last meeting", "what happened in my last meeting", "summarize the X meeting", or when notes are pasted.
---

## My setup

This section gets customized at install time. If placeholder text (`<...>`) is still present, ask the user to seed it before running.

### Format
<default: structured (decisions / action items / open questions / context). The user can pick "tight bullets" or "narrative paragraph" instead.>

### Hard cap (words)
<default: 400. The user can override.>

---

# Meeting Summary

Produce a clean meeting summary in the user's chosen format and append it to today's daily log.

## Sources you can pull from

The user can feed you a meeting three ways. Handle whichever fits:

1. **Granola via Composio** — pull transcripts directly through the Granola toolkit. Read-only.
2. **Another note-taking toolkit connected via Composio** — same shape (Otter, Fireflies, Fathom, etc.).
3. **Inline content** — the user pastes a transcript or raw notes directly. No tool call needed.

## Inputs

- The "My setup" section above (format and word cap)
- The meeting target (the user's stated request, or a meeting ID, or pasted content)
- `skills/meeting-summary/feedback.md` (recent reactions; honor them)

## Process

1. Identify the source. If the user pasted content, use it directly. Otherwise, use the connected toolkit to find the meeting (most recent, named, or by ID).
2. Pull the transcript via the toolkit if needed. If it's empty (the recording app may still be processing), tell the user to try again in a few minutes and stop.
3. Synthesize in the user's chosen format. Default fields: decisions, action items (with owners + due dates if mentioned), open questions, 2-3 sentence context. Cap at the word limit.
4. Append to today's daily log under `## Meeting: <title>`.
5. Send a short Telegram confirmation with title and counts (N decisions, N action items, N open questions).
6. When the user replies with feedback, append a dated free-form note to `feedback.md` and confirm: "Got it. I'll remember this for next time."

End every summary confirmation with: *Anything you want me to change for next time? What worked, what didn't?*

## Hard rules

- Never invent decisions, attendees, or action items not in the transcript or notes.
- Never include private side-comments or off-topic chatter.
- Never exceed the word cap from "My setup."
- Never overwrite an existing meeting summary in the daily log. Append with a timestamp suffix if the same meeting is summarized twice.

## When the summary fails

If the connected source is unreachable, log to `skills/meeting-summary/failures/YYYY-MM-DD.md` and send one short Telegram note: "Meeting summary failed: <reason>." Don't retry repeatedly.

If a transcript is empty (meeting too recent), tell the user to retry later. Not a failure; just a timing constraint.

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| Granola or other note-taking toolkits via Composio | Composio |
| Daily logs (`memory/YYYY-MM-DD.md`) | OpenClaw native |
| Inline "My setup" in SKILL.md | OpenClaw native pattern |
| `skills/meeting-summary/feedback.md` | Workshop addition |
| `skills/meeting-summary/failures/` | Workshop addition |
