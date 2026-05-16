# Set up the meeting notes summarizer

Help the user set up the `meeting-summary` skill so it produces clean structured summaries (decisions, action items, attendees, open questions) appended to today's daily log. Source flexibility is the point of this step: the user might use Granola, another note-taking app, or just paste raw notes. Walk them through picking the source, then install the skill and run one test.

If `meeting-summary` is already installed with a working source, ask the user whether to refine the source choice or just re-test.

## Step 1: ask the user which source they want to use

Ask the user, in plain language:

> Three ways to get meeting notes to me:
>
> 1. **Granola via Composio** — if you record meetings with Granola, I can pull transcripts directly. Cleanest if you already use Granola.
> 2. **Another note-taking app** — if you use Otter, Fireflies, Fathom, or something else, I can check if Composio supports it. Same flow as Granola if so.
> 3. **Paste your notes yourself** — drop a meeting transcript or raw notes into chat and I'll summarize them in the same format. No integration needed.
>
> Which one fits how you work? You can also pick more than one (e.g., Granola for recorded meetings, paste for ad-hoc notes).

Wait for their answer. Branch on it.

## Step 2: set up the source

### Path 1: Granola via Composio

Use Composio to link Granola (the user's browser opens for Granola OAuth consent; approve).

Tell the user, in plain language:

> Composio uses managed broad access at the OAuth layer. The "read-only, never modify or delete meetings" rule lives in the meeting-summary skill's hard rules, not at the Composio connection.

### Path 2: another note-taking app

Ask the user which app they use (Otter, Fireflies, Fathom, etc.). Check if Composio has a toolkit for it by inspecting the available Composio toolkits.

If a toolkit exists, walk them through linking it the same way as Granola. If no Composio toolkit exists for that app, fall back to Path 3 (paste).

### Path 3: paste notes directly

No integration to set up. Tell the user:

> When you want a summary, paste the meeting transcript or your raw notes into chat and ask me to summarize. I'll run them through the same meeting-summary skill I'd use for a Granola transcript.

Move directly to Step 4 (skip Step 3's source-verification step — there's nothing to verify because the user's pasted content IS the source).

## Step 3: verify the source works (Paths 1 and 2 only)

Quick sanity check. Ask the user:

> What was your most recent meeting? Let me list the last 5 from your connected source so we can pick one to summarize as a test.

Use the appropriate Composio action to list recent meetings. If the list comes back, the source is wired correctly.

## Step 4: confirm the meeting-summary skill is installed

Read `<workspace>/skills/meeting-summary/SKILL.md`. Confirm it exists.

If missing, tell the user to install it first and stop.

## Step 5: customize the "My setup" section

Open `<workspace>/skills/meeting-summary/SKILL.md`. The "My setup" section near the top has placeholder format and word cap.

Ask the user, both questions at once:

> Two quick questions for your meeting-summary setup:
> 1. What format do you want? Default is structured (decisions / action items / open questions / context). You can pick tight bullets or a narrative paragraph instead.
> 2. What's the word cap? Default is 400. You can override.

Wait for both answers.

Write the edit (update only the "My setup" section). Tell the user in one line what you captured — format and word cap.

## Step 6: run a test summary

### Paths 1 and 2 (connected source)

Run: "Summarize my last meeting." The skill reads its own "My setup" section and:

- Uses the connected source to list the user's recent meetings; picks the most recent
- Uses the same source to fetch the transcript
- Synthesizes in the user's chosen format
- Appends to today's daily log under `## Meeting: <title>`
- Sends a short Telegram ping confirming

### Path 3 (paste)

Ask the user to paste raw notes or a transcript from a real meeting they had recently. Then invoke meeting-summary with the pasted content as the source. The skill should:

- Synthesize the pasted content in the user's chosen format
- Append to today's daily log under `## Meeting: <title from notes, or "Untitled meeting">`
- Send a short Telegram ping confirming

Show the user the summary that was added to today's daily log.

## Step 7: stop

Stop when the test summary is in the daily log. Tell the user:

> meeting-summary is wired. Anytime you want a summary, say "summarize my last meeting" (if you set up a connected source), or paste your notes and ask me to summarize. The output always lands in today's daily log.
