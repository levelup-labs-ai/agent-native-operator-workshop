---
name: inbox-watch
description: Watch the user's inbox for emails matching their watchlist. When a match arrives, ping them with synthesized thread context and a draft reply in their voice. Designed for heartbeat wakeups so the agent stays state-aware across wakes via in-session memory. Use when the user says "watch my inbox for X" or when the configured heartbeat runs.
---

## My setup

This section gets customized at install time. If placeholder text (`<...>`) is still present, ask the user to seed it before running.

### Watchlist
<senders, domains, subject patterns, or keywords the user wants to be pinged about. Three to seven items.>

### Voice (default)
<the user's default tone for drafted replies. e.g., "warm but concise, no hedging">

### Voice for specific recipients (optional)
<recipient or recipient group → tone override.
e.g.,
- investors: warm but concise, no hedging
- engineering: direct, technical, jargon OK
>

---

# Inbox Watch

Watch the user's inbox for matches against their watchlist. Ping them with synthesized thread context and a draft reply. Designed for a HEARTBEAT trigger so you have in-session memory of what you've already pinged about (your dedup mechanism).

## Trust ceiling — read this every time you run

This skill touches **other people's inboxes** when it sends. That means the default trust ceiling is strict: **draft only, never send autonomously, never send without explicit per-email approval from the user.**

The rules:

1. You may save drafts to the user's Gmail drafts folder via Composio. Drafts stay in the user's account; they're reversible and they don't reach anyone until the user clicks send.
2. You may NOT send an email under any circumstances unless **both** of these are true:
   - The user has explicitly approved this specific email in the current conversation (e.g., "yes, send it" or "send that draft"), AND
   - You explicitly asked for that approval first in this conversation (don't act on a stale "go ahead" from earlier).
3. If you're unsure whether you have approval, you don't. Ask again. Treat ambiguous responses ("ok", "looks good", "sure") as approval to draft, not to send.
4. The "ask before sending" rule applies even if the underlying Composio Gmail connection technically allows send. The connection is broad on purpose; this skill's body is the ceiling.

## Inputs

- The "My setup" section above (watchlist + voice)
- Conversation history (your dedup; heartbeat shares the user's main session, so prior pings are visible)
- `skills/inbox-watch/feedback.md` (recent reactions; honor them)

## Process

1. Fetch unread Gmail through Composio. Scope by what's unread since your last wake.
2. Filter against the watchlist. Drop non-matches.
3. Dedup against your prior pings in this session's conversation history.
4. For each survivor, synthesize the thread context in ~3 lines (number of messages, when the user last replied, sender tone, prior commitments).
5. Draft a reply in the user's voice via Composio Gmail (save as a draft, **never send**). Capture the draft URL.
6. Send a Telegram ping with sender, subject, thread context, and the draft link. Tell the user the draft is saved and waiting in their Gmail. Do not offer to send it for them; let them open Gmail and send manually.
7. If nothing survives filtering, stay silent. Reply `HEARTBEAT_OK` so the harness logs the wake without messaging the user.
8. When the user replies with feedback, append a dated free-form note to `feedback.md` and confirm: "Got it. I'll remember this for next time."

End every match ping with: *Anything you want me to change for next time? What worked, what didn't?*

## If the user asks you to send

If the user replies to a match ping and explicitly says "send it" or "send that draft":

1. Confirm which draft you're sending (read back the recipient and subject so there's no ambiguity).
2. Ask once: "Sending to [recipient], subject [subject]. Confirm?"
3. Send only on a clear "yes" / "confirmed" / "do it" reply.

If the user says "send the next one too" or asks for blanket pre-approval, refuse. Approval is per-email, not per-watchlist. Explain why: "Send approval has to be per-email — I don't want to misjudge a borderline case. Drop me a 'send' when each one's ready and I'll confirm and send."

## Hard rules

- Never send an email without explicit per-email user approval (see the Trust ceiling section).
- Never re-ping about a message you already pinged in this session.
- Never invent thread context. Synthesize only what's in the thread.
- Never include full message bodies in the ping. Synthesis only.
- Never accept blanket pre-approval to send multiple emails. Each send is a separate approval.

## When the watch fails

If Composio Gmail is unreachable, don't send a "watch failed" ping every wake. Log to `skills/inbox-watch/failures/YYYY-MM-DD.md`. Send one ping the first time the failure occurs, then stay silent until it clears or the user asks.

## Why heartbeat, not cron

This skill needs memory of what it already pinged about within a session. Wired to a cron, every fire would be a fresh session with no memory, so dedup can't work. Heartbeat keeps you in the user's main session.

## Graduation to Automate

The skill operates at AUGMENT trust. To graduate to AUTOMATE (send after drafting without asking) after `skill-review` validates the pattern:

1. Change the hard rule from "ask before sending" to "send after drafting."
2. Update Step 5 to send the email after composing.
3. Update the ping wording from "Draft reply ready" to "Reply sent."

Three edits, all in this file. The Composio connection doesn't change.

Demotion works the same way in reverse.

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| Heartbeat in-session memory (dedup) | OpenClaw native |
| Inline "My setup" section in SKILL.md | OpenClaw native pattern |
| Composio Gmail toolkit | Composio |
| `skills/inbox-watch/feedback.md` | Workshop addition |
| `skills/inbox-watch/failures/` | Workshop addition |
| Trust levels (Augment / Automate) | Workshop framing, enforced by the "ask before sending" hard rule |
