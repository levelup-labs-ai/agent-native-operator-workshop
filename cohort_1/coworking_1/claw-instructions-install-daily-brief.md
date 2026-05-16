# Install daily-brief

Install the daily-brief skill into this workspace. The recipe lives at `<workspace>/skills/daily-brief/SKILL.md`. What you're doing now is filling in the "My setup" section at the top of that SKILL.md with the user's preferences, then running one test and verifying the feedback loop works end to end.

If the SKILL.md isn't in the workspace, stop and tell the user to install it first.

If `<workspace>/skills/daily-brief/SKILL.md` already has "My setup" filled in (no `<...>` placeholders remaining), ask the user whether to skip personalization or refine.

## Step 1: gather preferences in 2 batches of 2

Ask the four questions in two batches. Wait for both answers in each batch before sending the next.

**Batch 1 — what to cover:**

> Two quick questions about what to cover:
> 1. What topics do you actually want covered in your daily brief? Three to five things you'd genuinely read every morning.
> 2. What sources should I pull from? Default is general web search. Want me to narrow it to specific blogs, newsletters, or accounts? (Note: X profiles, LinkedIn feeds, and paywalled sites usually don't scrape cleanly. Stick to open web.)

Wait for both answers.

**Batch 2 — shape and skip:**

> Two more:
> 3. What format do you want? Default is 3 buckets — must read / worth knowing / skip. Or you can pick a short paragraph or 5 flat bullets.
> 4. What should I explicitly skip? Topics that are noise for you.

Wait for both answers.

## Step 2: propose the SKILL.md edit

Based on the answers, propose an edit to the "My setup" section of `<workspace>/skills/daily-brief/SKILL.md`. Replace each `<...>` placeholder with the user's answer:

```markdown
## My setup

### Topics I want covered
- <user's topics>

### Sources
<user's source preference>

### Format
<user's format choice>

### Topics to skip
- <user's skip list>
```

Write the edit (update only the "My setup" section; don't rewrite the rest of the SKILL.md). Tell the user in one line what you captured — topics, sources, format, skip list.

## Step 3: run one test brief

Run `/daily-brief now`. The skill reads its own "My setup" section and produces a brief on the user's topics, in the format they chose, sent to Telegram.

If the brief fails (no novel items found, sources empty, etc.), do NOT send a broken brief. Log the failure and tell the user the reason.

## Step 4: capture feedback and verify the loop

After the test brief lands, the brief's own output (per the SKILL.md body) invites the user to react. Wait for their reaction.

When the user replies with feedback (anything in their own words — "too long today", "skip image-gen news", "loved the must-read section", or even just "good"), append the reply to `skills/daily-brief/feedback.md` as a dated free-form note, then confirm in chat:

> Got it. I'll remember this for next time.

Don't expose the file path or contents to the user unless they ask.

Then suggest verification, so the user knows the loop works:

> If you want to double-check it landed, ask me "did you record that feedback?" and I'll confirm.

If the user asks for that confirmation, read `skills/daily-brief/feedback.md`, find their entry, and tell them: "Yes — saved it under today's date. Run `/skill-review` any time to review patterns and get proposals."

## Step 5: mention skill-review (one line)

Tell the user, once:

> When your feedback on a skill starts to accumulate, run `/skill-review` and it will read everything you've given and propose structural changes for your approval.

## Step 6: stop

Stop when the user has seen the test brief land, given at least one piece of feedback, and seen confirmation that it was recorded.
