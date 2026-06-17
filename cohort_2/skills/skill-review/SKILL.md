---
name: skill-review
description: On-demand review of all skills' feedback logs. Reads feedback.md files across the workspace, identifies patterns, and proposes SKILL.md updates for user approval. Invoke manually with /skill-review, or /skill-review [skill-name] for a specific skill.
---

# Skill Review

You audit the user's skills on demand by reading their accumulated feedback and proposing structural improvements. The user approves; you edit. You never edit a SKILL.md without explicit approval.

## Feedback tagging convention

Each entry in a skill's `feedback.md` carries an inline status tag once you've processed it. Three states:

- `[FIXED YYYY-MM-DD: <one-line change>]` — you proposed an edit based on this entry; the user approved; you applied the edit
- `[SKIPPED YYYY-MM-DD: <one-line reason>]` — you reviewed this entry but took no action (below threshold, user said no, ambiguous, etc.)
- (no tag) — not yet reviewed

You only act on untagged entries. Once tagged, an entry is "done" and you don't surface it again unless the same theme keeps recurring in fresh entries.

## Process

### Step 1: Find skills with untagged feedback

Scan `skills/*/feedback.md`. For each file, count the entries that have no `[FIXED ...]` or `[SKIPPED ...]` tag yet. If a skill has zero untagged entries, skip it for this run.

### Step 2: For each skill with untagged feedback

Read the corresponding `skills/<skill-name>/SKILL.md`. Read the untagged feedback entries (dated free-form notes). Identify patterns:

- Recurring concerns (the same theme appears in 3+ entries)
- Recurring positive signals (consistent praise themes)
- Format drift (length issues, missing sections, wrong tone)
- Off-topic items (categories the user keeps flagging)
- Missed items (things the user said should have been included)

Don't propose updates for one-off feedback. The threshold is "3+ untagged entries in the same direction." One-offs are noise — they get a `[SKIPPED ... below threshold]` tag at the end of the run.

### Step 3: Propose specific SKILL.md edits

For each pattern, write a specific edit proposal: which line to change, what to add, what to remove. Be concrete. "Tighten the format" is not a proposal; "add to Hard Rules: Skip image-gen items" is.

### Step 4: Surface to the user

Send one Telegram message per skill with the proposals. Format:

```
[skill-review] Proposals for `daily-brief`:

Patterns I noticed (last 14 days, 23 untagged entries):
- Image-gen items flagged as noise: 5 entries
- Brief too long: 4 entries
- Positive on the research/product split: 7 entries (no action needed)
- Anthropic blog repeated: 2 entries (below threshold; I'll mark as SKIPPED)

Proposed SKILL.md changes:
1. Add to Hard Rules: "Skip image-gen items"
2. Tighten word cap from 600 → 500
3. Extend dedup lookback from 7 → 14 days

Reply: y (approve all), 1 / 2 / 3 (specific items only), n (skip all)
```

### Step 5: On approval

Wait for the user's reply. On approval (full or partial):

1. Edit the SKILL.md per the approved changes (use `file_edit`, not `file_write` — preserve everything else).
2. For each entry that drove an approved change, append an inline `[FIXED YYYY-MM-DD: <one-line change>]` tag at the end of that entry in feedback.md.
3. For entries that fell below threshold or aren't being acted on, append `[SKIPPED YYYY-MM-DD: <reason>]`.
4. Confirm to the user with what was edited and how many feedback entries got tagged FIXED vs SKIPPED.

### Step 6: On reject or no response

If the user replies `n` or doesn't respond within 48 hours:

1. Don't edit the SKILL.md.
2. Append `[SKIPPED YYYY-MM-DD: user declined this cycle]` to each entry that was part of the proposal. (User explicitly said no, or didn't act — either way, don't keep surfacing the same entries.)

If the same patterns keep recurring in fresh untagged entries on later runs, surface them again with a note: "I flagged this <N> weeks ago and you skipped. Same theme is still showing up — want me to revisit?"

### Step 7: invite + listen for meta-feedback

After the per-skill messages are sent and the user has replied to all of them, end the review session with:

> Anything you want me to change about how I review your feedback? What worked, what didn't?

When the user replies with meta-feedback, append a dated free-form note to `skills/skill-review/feedback.md` and confirm: "Got it. I'll remember this for next time." (This is the meta-meta loop — `skill-review` gets reviewed too, by itself, over time.)

## Hard rules

- Never edit a SKILL.md without explicit user approval.
- Never delete or rewrite feedback.md entries. Only append inline `[FIXED ...]` or `[SKIPPED ...]` tags at the end of existing entries.
- Never propose changes that contradict the skill's stated intent in the SKILL.md frontmatter description.
- Never propose more than 5 changes per skill in a single review message — pick the highest-signal patterns.
- Never tag entries `[FIXED ...]` without actually applying the edit. The tag means the change is live in the SKILL.md.

## When the user invokes for a specific skill

If invoked with `/skill-review <skill-name>`, run the same process for just that one skill — useful after a noticeable run of bad outputs on one skill without wanting to review everything.

## Cross-skill patterns (advanced)

If you notice a pattern that affects multiple skills (e.g., the user consistently asks for shorter outputs across 3 different skills), surface it as a separate "global pattern" message:

```
[skill-review] Cross-skill pattern noticed:

Across daily-brief, research-decision, and meeting-prep, the user
has asked for shorter outputs 9 times in the last two weeks.

Proposed: add a global response default in AGENTS.md:
"Default response length: under 400 words unless the user asks for depth."

Reply y or n.
```

On approval, edit AGENTS.md and tag the contributing entries `[FIXED YYYY-MM-DD: global response-length default added to AGENTS.md]` across the relevant skills' feedback files.
