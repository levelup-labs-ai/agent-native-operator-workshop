# Set up plan-my-day with Google Calendar

Connect the Google Calendar toolkit (typically via Composio if that's what the user has set up). Configure the `plan-my-day` skill at `<workspace>/skills/plan-my-day/` by filling in its "My setup" section. Ask the user for their working hours and this-week priorities directly. Run one test.

If the SKILL.md "My setup" is already filled in (no `<...>` placeholders), ask the user whether to refine or skip.

## Step 1: confirm the workspace is ready

Inspect what's installed. Confirm `<workspace>/skills/plan-my-day/SKILL.md` exists and that the user has a Gmail / Google Calendar integration reachable (Composio is the workshop default). If anything's missing, walk them through installing or connecting it before continuing.

## Step 2: connect Google Calendar through Composio

Use Composio to link Google Calendar. The user's browser opens for Google's OAuth consent. Approve. Same managed-broad-access model as Gmail. The read-only ceiling for this skill is enforced by the skill's hard rules (`Never modify or delete calendar events`), not by the Composio connection.

## Step 3: verify calendar READ works

Ask the user: "What's on my calendar today?" Use Composio Google Calendar to fetch today's events. If today's events come back, calendar access is working.

## Step 4: ask for working hours and this-week priorities

Don't assume the user has filled in USER.md with their working hours or priorities. Ask them directly, both questions at once:

> Two quick questions so I can plan your day well:
> 1. What are your working hours today? (e.g., "9am-6pm Pacific")
> 2. What are the 2-4 things on your plate this week that you'd want focused time for? One short bullet each.

Wait for both answers. You'll feed these into the plan at runtime.

If the user wants to save these for later runs (so they don't have to answer every time), offer:

> Want me to save these in your USER.md so I remember next time? I'll add a Focus section (your priorities this week) and a Patterns section (your working hours). You can change them anytime.

If they say yes, write a Focus section (priorities) and a Patterns section (working hours) into USER.md and tell them in one line what you saved. If they say no, just use the answers for today's plan.

## Step 5: customize the "My setup" section

Open `<workspace>/skills/plan-my-day/SKILL.md`. The "My setup" section near the top has placeholder format and word cap.

Ask the user, both questions at once:

> Two more quick questions for the plan-my-day setup:
> 1. What format do you want for the daily plan? Default is schedule + focus blocks + heads-up + first move. Or you can pick a flat list or a paragraph.
> 2. What's the word cap? Default is 350.

Wait for both answers.

Write the edit (update only the "My setup" section). Tell the user in one line what you captured — plan format and word cap.

(Delivery time is NOT in the skill's My setup. The skill runs on demand or via the morning cron. The cron in Step 7 controls when. The output channel defaults to Telegram.)

## Step 6: test once

Run "/plan-my-day" or "Plan my day." The skill reads:
- Its own "My setup" section (format and word cap)
- Today's calendar via Composio Google Calendar
- The working hours and priorities you captured in Step 4 (from USER.md if saved, or from this session's answers if not)

It identifies focus blocks in calendar gaps >= 60 min within working hours, formats the plan, and sends to Telegram.

## Step 7: optional — wire morning cron

If the user wants the plan to arrive automatically each morning, ask for the cron time and explain in plain language what you'll set up:

> A cron called "Plan my day" that fires at 7am weekdays in your timezone, runs in a fresh isolated session, and sends today's plan on Telegram using your configured format.

Show the next 3 fire times so the user can sanity-check. On approval, register the cron with these parameters (use whatever shape your harness's cron CLI takes):

- name: Plan my day
- the user's chosen cron expression
- the user's timezone
- a fresh, isolated session per fire (so cron runs don't pollute the main chat)
- a deterministic message that names the skill and channel explicitly (so the agent doesn't drift on what to do when the cron fires)

Don't pass delivery channel or chat ID. The harness should resolve those from connected state.

Verify the cron is live in the scheduler before claiming success.

## Step 8: stop

Stop when the test plan is on Telegram. Tell the user:

> plan-my-day is wired. Tomorrow morning at <time>, your daily plan will land on Telegram. I'll remember any feedback you give me on the plans — run `/skill-review` whenever you want to see proposals based on what you've noted.
