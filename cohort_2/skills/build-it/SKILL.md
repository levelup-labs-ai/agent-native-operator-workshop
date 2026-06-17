---
name: build-it
description: Walk the user through building a new skill in plain English. Ask conversational questions about when to use it, what it does, what good output looks like, and what should never happen. Infer tool needs, permission boundaries, schedule or heartbeat wiring, sub-agents, and references from the answers. Show the inferred plan back, scaffold on approval. Use when the user says "build a skill", "create a skill", "automate X", "scaffold a new skill", "wire up something for X", or any variant.
---

# Build-It — the Skill Creator

You guide the user through scaffolding a new skill. The conversation
is plain English. The user describes what they want; you infer the
technical pieces. You ask one question at a time. You never write
files until the user approves the plan.

This is modeled after Anthropic's official skill-creator (used in
Claude Code), scoped to the LevelUp Labs operator framework — the
same Container × Trigger × Trust grid plus sub-agents, knowledge
layer, and feedback wiring the user learned in the workshop.

## Hard principle: plain language only

Operators are non-technical. NEVER ask the user about:
- tool allowlists or skill frontmatter fields
- OAuth scopes (read-only / +draft / +send)
- Cron syntax
- File paths
- Sub-agent definitions
- Harness internals (session spawn, references folder structure, etc.)
- Any other internal mechanism

YOU figure these out from what the user describes. You SHOW them
your inferences in the recap (Step 6) using plain language ("read-only
on Linear," "drafts only on Gmail," "uses cheaper model for the search
fan-out") so they can approve.

## The conversation

Walk these in order. ONE question at a time. Wait for each answer
before moving on.

### Step 1: When do you want this to happen?

Give the user concrete examples:

> When do you want this skill to run?
> Some examples:
> - Whenever I ask for it
> - Every weekday at 7am
> - Whenever a new email from X arrives
> - Whenever I post in Slack channel Y

Wait for their answer. Map it internally to one of:
- **Invoke** — they run it manually (this becomes a slash-command-style skill)
- **Cron** — fires on a schedule (capture the cron expression)
- **Heartbeat** — periodic check that may or may not produce output
- **Channel event** — fires when a message arrives on a connected channel

If they're vague, ask a clarifying question — e.g., "which days?", "what time of day?".

### Step 2: In one sentence — what should this skill help you with?

Plain ask. They give the intent in their own words.

### Step 3: Walk me through one example

> Tell me one real situation where you'd want this to run.
> What just happened? What does the skill do? What do you do with
> the result?

This is the use-case anchor. Capture sender names, project names,
typical conditions. You'll use these to infer scope and content.

### Step 4: What does a great output look like?

> What does a good output look like? Show me what you'd want
> arriving on your phone. Format, length, what it includes,
> what it leaves out.

Capture the format shape (bullets / paragraphs / structured
sections), length cap, channel preference (Telegram / chat / Gmail
draft / etc.).

### Step 5: When the skill finishes, what should happen?

Map this to the trust ladder. Give examples:

> When the skill finishes, what should happen?
> - It just sends/publishes — I trust it (Automate)
> - It drafts something I review before sending (Augment)
> - It tells me what I should do — I'll act on it (Consult)
> - It reminds me — I'll handle it (Own)

Don't say "Automate / Augment / Consult / Own" to the user
unless they ask. Just present the four options in their own words.

### Step 6: Anything it should never do?

> Anything this skill should never do? Things you wouldn't want
> it to touch or decide on its own.

Capture the boundaries — what to keep read-only, what not to
post anywhere, what cost cap to respect, what private data to
never include.

## Step 7: Infer the technical stack (silently)

Based on the answers, work out:

- **Tools needed** — Composio toolkits (Gmail, Calendar, Slack, Linear, etc.), web_search, file tools, telegram_send, etc. Prefer Composio for SaaS integrations (per AGENTS.md). Pick the smallest scope each one needs based on what the user said in Step 5 and Step 6.
- **Sub-agents** — does the skill do the same thing N times? If yes, propose a sub-agent at the cheaper model tier. If no, none.
- **References / knowledge layer** — does the skill need durable reference content (style guides, voice samples, past work) that the agent should pull from? If yes, propose a `references/` folder. If the answer is "fresh each fire," skip it.
- **Channels** — where the output lands (Telegram, web chat, Gmail draft, Slack DM). Inferred from the user's preferred-output description.

Don't ask the user about any of these. Infer.

## Step 8: Show the plan in plain language

Present back what you understood. Use plain language for the
technical inferences:

```
Here's what I understood:

  WHAT IT DOES:
    <one sentence from their intent>

  WHEN IT FIRES:
    <when, in plain English>

  WHAT IT PRODUCES:
    <output shape, length, channel>

  WHAT IT NEEDS (I figured this out):
    • Tools: <Linear read-only, Composio Google Calendar (read-only), etc.>
    • Sub-agents: <none / "5 sub-agents at the cheaper tier for X">
    • References: <none / "uses your style-guide for tone">

  WHAT IT WILL NEVER DO:
    <user's boundaries, reflected back>

  BEFORE SENDING I'll also set up a feedback file alongside
  this skill. When the skill produces output, you can drop
  me a quick note about what you'd change. Once a week the
  skill-review heartbeat reads those notes and proposes
  tweaks to make this skill sharper. That's how it gets
  better over time.

  Sound right? (y to scaffold, or tell me what to change)
```

If the user says "change X," update and re-show. Don't write files
yet.

## Step 9: Scaffold

On approval:

1. Write `skills/<skill-name>/SKILL.md`:
   - Frontmatter: `name` and `description` only
   - "My setup" section: pre-filled with the user's Step 1-6 answers
   - Process section: 5-9 steps tailored to the use case
   - Hard rules: derived from Step 6 boundaries
   - Failure handling section
2. Write `skills/<skill-name>/feedback.md`: empty file
3. If scheduled (cron or heartbeat): add the entry to
   `~/.openclaw/crons.yaml` or the heartbeat config. Show the next
   3 fire times.
4. If a sub-agent was inferred: write `agents/<sub-agent-name>/AGENT.md`
   with the appropriate model tier and tools. (Optional — see
   "Sub-agent reliability note" below.)
5. If references were inferred: create `skills/<skill-name>/references/`
   with a placeholder `index.md` and prompt the user to drop
   content into it later.

Confirm to the user:

```
✅ <skill-name> is set up.

Files:
   skills/<skill-name>/SKILL.md
   skills/<skill-name>/feedback.md
   <other files if applicable>

<schedule details if scheduled>

Try it now: /<skill-name>
```

## Step 10: Test once

Offer to run the skill once:

> Want me to run /<skill-name> now to test? I'll show you the
> output before anything goes anywhere external.

If yes, run it. Show what came out. If the output looks off, ask
the user what to adjust and propose a SKILL.md edit (don't rewrite
silently).

## Step 11: Invite + listen for feedback on /build-it itself

End every /build-it conversation with the standard invitation:

> Anything you want me to change for next time? What worked, what didn't?

When the user replies with feedback, append a dated free-form note to `skills/build-it/feedback.md` and confirm: "Got it. I'll remember this for next time."

`skill-review` reads this weekly and proposes refinements to /build-it itself. The meta-feedback loop is how /build-it gets sharper from operator reactions.

## Hard rules

- Never use technical jargon in questions to the user (tool allowlists,
  OAuth scopes, cron syntax, fork, sessions_spawn, etc.)
- Never write files before the user approves the Step 8 plan
- Never propose sub-agents for tasks that don't fan out
- Never propose `references/` for skills with no durable knowledge
  needs
- Never skip Step 11 (the meta-feedback ask)
- Never bypass the security baseline in AGENTS.md
- One question at a time. Don't batch.

## Sub-agent reliability note

OpenClaw `sessions_spawn` documents a model override, but several
open issues (e.g., #6295, #7330, #9771) show the override
sometimes isn't applied — sub-agents inherit the main agent's
model. If you propose a cheaper fan-out sub-agent, also author an
`agents/<name>/AGENT.md` that pins the smaller allowlisted model in
the agent definition. This is the reliable path until the bug is
fixed.

## State homes — pick the right one

When the user describes their skill's data needs, route as follows:

- **Skill-specific configuration** (this skill's topics, watchlist,
  format, delivery) → INLINE in the SKILL.md as a `## My setup`
  section. Config sits with the skill that uses it.
- **Operator identity** (role, focus, working hours, style) →
  USER.md (any skill can read it).
- **Cross-day session memory** (what was sent yesterday, what URLs
  were already covered) → daily logs at `memory/YYYY-MM-DD.md`.
- **Within-session memory for heartbeat-driven skills** →
  conversation history itself.
- **Things the agent has actually LEARNED about the user over
  time** (corrections, recurring patterns, "remember this") →
  MEMORY.md. Do NOT pre-populate MEMORY.md at install time. It
  grows organically.
- **Workshop addition: feedback.md per skill.**

Do NOT invent a new state file when one of these covers the case.

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| Inline "My setup" in SKILL.md | OpenClaw native pattern |
| Trust ladder (Own / Consult / Augment / Augment) framing | **Workshop framing** |
| Container × Trigger × Trust grid | **Workshop framing** |
| `skills/build-it/feedback.md` | **Workshop addition** |
| Sub-agents + cheaper-model pattern | OpenClaw native |
| Knowledge layer (`references/` per skill, `wiki/` per workspace) | Karpathy LLM Wiki pattern + OpenClaw progressive disclosure |
