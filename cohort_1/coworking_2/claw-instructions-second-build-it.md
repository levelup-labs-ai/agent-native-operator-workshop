# Second /build-it pass

The user has run `/build-it` before and wants to scaffold another skill. Same conversational walk, faster the second time because they already know the question shape.

## Step 1: ask which skill this time

If the user wants suggestions, offer a list (pick anything different from what they've already built). Otherwise just ask:

> Which skill do you want to scaffold this time?

Common picks: weekly-recap, meeting-prep, standup-writer, expense-categorizer, read-later, customer-followup. Or whatever the user describes.

## Step 2: run /build-it

Run the `/build-it` flow as usual. The user already knows the question shape; walk faster. Ask plain-language questions, infer the stack, present back, scaffold on approval.

If the user's second skill overlaps with one they've already built (e.g., both pull from Linear, both use Composio Google Calendar), point that out: "This skill needs the same tools as <previous>. No new connections to set up."

## Step 3: scaffold and test

Same as a normal `/build-it` run: write the SKILL.md, seed feedback.md, write any cron or heartbeat config, test once with a real run.

## Step 4: ask for /build-it feedback (especially this time)

Ask:

> How was this pass compared to the previous one? Anything that felt redundant, anything missing? Feedback on `/build-it` itself makes it sharper.

Append the response to `skills/build-it/feedback.md` as a dated free-form note. The repeated-pass observations are particularly valuable for the meta-feedback loop.

## Step 5: stop

Stop when the test run completes. Tell the user:

"You've scaffolded another skill yourself. The pattern compounds — each new skill is a smaller lift than the last. The framework is yours to extend."
