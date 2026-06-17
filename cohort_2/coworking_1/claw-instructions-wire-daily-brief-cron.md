# Wire the daily-brief cron

Schedule daily-brief to fire on the user's preferred cadence. Register the cron in the live scheduler (not just on disk). Verify it's live before claiming success. Before registering the cron, make sure there's an actual Telegram destination available.

If a daily-brief cron is already wired, ask the user whether to skip or replace.

## Step 1: confirm cadence

Ask the user:

> What time and which days do you want daily-brief to fire? Common picks: 7am every weekday, 6:30am Mon–Fri, 8am every day.

Wait for the answer. Convert it into a cron expression in their timezone (read the timezone from MEMORY.md or USER.md if set).

## Step 2: explain the setup in plain language

Tell the user, in natural language, what you're about to register. Don't show raw flags or YAML:

> Here's what I'll set up:
> - A cron called "Daily brief" that fires at <cadence> in your timezone
> - Each fire spins up a fresh isolated session, so the cron run can't pollute our chat
> - That session runs your daily-brief skill and sends today's brief on Telegram using the topics, sources, format, dedup rules, and skip list you configured earlier
>
> Next 3 fires would land at: <time 1>, <time 2>, <time 3>. Look right?

When deciding delivery:

- If the current conversation is already on Telegram, bind that chat explicitly as the cron delivery target.
- If the current conversation is not on Telegram and there is no known Telegram target yet, ask the user to send one message from Telegram first (or provide the target chat) before claiming the cron is wired.
- Avoid guessing or inventing a Telegram target from unrelated session metadata.

Prefer explicit delivery fields when the route is known:

- `delivery.mode = "announce"`
- `delivery.channel = "telegram"`
- `delivery.to = "telegram:<chatId>"`

If no Telegram route is known yet, stop after explaining what's missing rather than registering a cron that will fail closed.

## Step 3: register the cron

On approval, register the cron with these parameters (use whatever shape your harness's cron CLI takes):

- name: Daily brief
- the user's chosen cron expression
- the user's timezone
- a fresh, isolated session per fire (so cron runs don't pollute the main chat)
- a deterministic message that names the skill and channel explicitly, so the agent doesn't drift on what to do when the cron fires (something like: "Run the daily-brief skill now and send today's brief on Telegram using the configured topics, sources, format, dedup rules, and skip list.")
- explicit Telegram delivery when a Telegram route is known

If no Telegram route is known yet, stop after explaining what's missing rather than registering a cron that will fail closed.

## Step 4: verify the cron is live

Check the live scheduler. Confirm a job named "Daily brief" exists, is enabled, and the next fire time matches what you proposed in Step 2. Also verify the scheduler's delivery preview shows an explicit Telegram target. If it doesn't, the cron is not fully wired yet.

If the cron doesn't appear in the live scheduler, tell the user honestly. Possible causes: the registration command errored silently, the cron expression has a syntax issue, or the harness found a duplicate. Don't pretend it worked.

## Step 5: confirm to the user

If the cron is live:

> Cron is live. Next fire: <time>. The OpenClaw GUI should now show "Daily brief" in the cron list. If you want to test before the first scheduled fire, type "Run /daily-brief now."

## Step 6: stop

Stop when the live scheduler confirms the cron is registered with an explicit Telegram target. Don't claim success based on the registration command not erroring; verify against the live scheduler.
