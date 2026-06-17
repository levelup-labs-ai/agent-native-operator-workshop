# Run /build-it for a real skill

Help the user scaffold a new skill on their bot using `/build-it`. Run it conversationally. Walk the plain-language questions. Infer the technical stack. Scaffold the files. Test once.

## Step 1: confirm /build-it is available

Check `<workspace>/skills/build-it/SKILL.md`. If missing, tell the user: "I don't see /build-it installed. Want me to install it?" Install on approval.

## Step 2: ask which skill the user wants to build

Show the user this list of common operator skills to choose from:

- **weekly-recap** — Friday wrap-up of what shipped/slipped/follow-ups (Linear + calendar)
- **meeting-prep** — Pre-meeting briefing for tomorrow's calls (calendar + Slack threads)
- **standup-writer** — Daily standup auto-drafted from yesterday's activity
- **expense-categorizer** — Triage expense emails into categories
- **read-later** — Weekly digest of articles the user saved
- **customer-followup** — Surface customers who haven't replied, draft follow-ups

Ask: "Pick one of these or describe your own. Whichever skill would actually save you time this week."

Wait for the user's pick.

## Step 3: run /build-it

Invoke `/build-it` with the user's chosen skill as the topic. Walk the conversation:

1. **When do you want this to happen?** (offer examples: "whenever I ask," "every weekday at 7am," "whenever an email from X arrives," "whenever I post in a Slack channel")
2. **In one sentence — what should this skill help you with?**
3. **Walk me through one example. What's a real situation where you'd want this to run?**
4. **What does a great output look like? Show me what you'd want to see arrive on your phone.**
5. **When the skill finishes, what should happen?** (offer examples: "just sends — I trust it," "drafts something I review," "tells me what to do — I act on it," "reminds me — I'll handle it")
6. **Anything it should never do?**

Ask one question at a time. Wait for each answer.

## Step 4: present the inferred stack

Based on the answers, infer:

- **Tools needed** (Linear? Composio toolkits? Granola? others? — pick what's needed; don't ask the user about specific OAuth scopes)
- **Sub-agents needed** (does it fan out N times? if yes, propose a sub-agent; otherwise none)
- **Model tier** (full-strength model for synthesis, cheaper model for fan-out workers; reserve the most expensive tier only for tasks that really need deep multi-step reasoning)
- **References / wiki** (does it need durable knowledge it didn't generate? if yes, propose a `references/` folder; otherwise none)
- **What it will never do** (echoed from the user's answer, in plain language)

Present back as a "Here's what I understood" recap in plain language. Also note that you'll set up a feedback file alongside the skill.

Wait for approval ("y", "looks right", or "change X"). Adjust if needed.

## Step 5: scaffold

On approval, write:

- `skills/<skill-name>/SKILL.md` — frontmatter with `name` + `description`, "My setup" section pre-filled with the user's answers, process steps that match the intent
- `skills/<skill-name>/feedback.md` — empty (per the Skill Creation rule in AGENTS.md)
- Cron or heartbeat registration (if scheduled) via the appropriate harness CLI

Confirm to the user with the file paths and the next-fire time (if scheduled).

## Step 6: test once

Run the new skill manually:

> /<skill-name> now

Watch it execute. The output should land where the user expects (Telegram, drafts folder, etc.). Capture session-cost if relevant.

If something fails, debug with the user — don't silently fix. The first run is part of the lesson.

## Step 7: ask for feedback on /build-it itself

Ask the user: "How was that conversation? Anything missing, anything I should have asked but didn't?" Append the response to `skills/build-it/feedback.md`. This is the meta-feedback loop — /build-it gets sharper from operator reactions.

## Step 8: stop

Stop when the test run completed. Tell the user:

"<skill-name> is wired. <Next-fire-info>. I'll remember any feedback you give it — run /skill-review whenever you want proposals based on what you've noted."
