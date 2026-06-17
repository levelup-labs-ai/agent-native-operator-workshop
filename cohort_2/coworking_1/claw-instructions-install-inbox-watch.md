# Install inbox-watch as a heartbeat

Set up inbox-watch by filling in the "My setup" section of `<workspace>/skills/inbox-watch/SKILL.md` with the user's watchlist and voice preferences, then wire it as a heartbeat (default cadence: every 30 minutes).

This skill needs Gmail reachable through the user's connected Gmail integration. If Gmail isn't reachable, walk them through connecting it first. The trust ceiling, "ask before sending," is enforced by the skill's hard rules, not by the underlying tool.

If the SKILL.md "My setup" is already filled in (no `<...>` placeholders), ask the user whether to skip personalization or refine.

## Step 1: gather watchlist and voice in 2 batches of 2

Ask the four questions in two batches. Wait for both answers in each batch before sending the next.

**Batch 1 — what to watch + whether to draft:**

> Two quick questions:
> 1. Who or what should I watch for in your inbox? Specific senders (people or domains), subject patterns, labels, or keywords. Three to seven things is plenty.
> 2. Do you want me to draft reply emails when matches arrive? If yes, drafts land in your Gmail drafts folder; you review and send manually.

Wait for both answers.

**Batch 2 — voice (only if drafting):**

If they said yes to drafting in Batch 1, ask:

> Two more on voice:
> 3. What's your default voice for replies? (e.g., 'warm but concise', 'formal', 'quick and direct')
> 4. Any specific recipients or recipient groups where you want a different voice? (e.g., investors warmer, engineering more direct)

Wait for both answers.

If they said no to drafting in Batch 1, skip Batch 2 entirely.

## Step 2: propose the SKILL.md edit

Based on the answers, propose an edit to the "My setup" section of `<workspace>/skills/inbox-watch/SKILL.md`. Replace each `<...>` placeholder.

If the user said no to drafting, leave Voice fields with their default-only line and note in the diff: "Drafting enabled but with default voice only — recipient-specific voices can be added later."

Write the edit (update only the "My setup" section; don't rewrite the rest of the SKILL.md). Tell the user in one line what you captured — watchlist and voice settings.

## Step 3: test the skill once manually

Before wiring the heartbeat, fire `inbox-watch` once in the current chat so the user sees what a match + draft looks like. Don't schedule something the user has never seen run.

Tell the user, in plain language:

> Before we schedule this to run regularly, let's fire it once now so you can see what a match looks like and check the draft quality. If anything's off — voice, who you're watching, what counts as a match — we'll refine your "My setup" before wiring the heartbeat.

Three possible outcomes:

- **Matches found, drafts produced.** Show the user the matched senders and the draft links. Ask: "Look right? Drafts in your voice? Anything to refine before we schedule it?" If they want changes, loop back to Step 2.
- **Matches found, no drafts.** Surface that drafts didn't generate. Diagnose before moving on.
- **No matches today.** Say so honestly. Offer two options: (a) wire the heartbeat now and treat the first real match as the live test, or (b) wait, send yourself a test email matching the watchlist, and re-run.

Don't proceed to Step 4 until the user has seen the skill's output (real or synthetic) and signed off.

## Step 4: wire the heartbeat

Propose a heartbeat entry with these parameters:

- **skill:** inbox-watch
- **cadence:** every 30 minutes (default; the user can override)
- **session:** main, so the skill has in-session memory across wakes (this is what enables dedup against prior pings)
- **trust:** augment, so the skill drafts but never sends autonomously (the SKILL.md hard rule enforces this)

Describe the proposed config in plain language and use whatever shape the harness's heartbeat config takes. Show the next wake time. On approval, write the heartbeat config.

## Step 5: stop

Stop when the heartbeat is wired. Tell the user:

"Inbox watch is live. The next match in your inbox triggers a Telegram ping with a draft reply linked. After about two weeks of consistent good drafts, `skill-review` will propose graduating inbox-watch to AUTOMATE — one edit to the SKILL.md hard rule (from 'ask before sending' to 'send after drafting'). The underlying Gmail connection doesn't change; only the skill's policy."
