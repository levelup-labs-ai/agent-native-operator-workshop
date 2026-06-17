# Build and query a wiki

Help the user build a knowledge wiki from a set of source documents and then query it. They get to see both halves: the compile (raw documents → structured wiki) and the query (asking a question and getting a grounded answer with cross-doc citations).

The cost/quality comparison story lives in the lecture. Here in the coworking, the user walks the full pipeline once on the workshop's synthetic startup workspace (Foxglove Co.).

## Step 1: confirm the raw docs are present

Check `wiki/raw/`. The workshop ships 24 synthetic workspace docs there — 3 weeks of a fictional startup's daily logs, meeting notes, memos, sales docs, and task snapshots. If the directory is empty or missing, tell the user: "The raw docs aren't in your workspace. Run the Step 1 load paste-prompt from `coworking_1/build.md` to pull them, then come back."

## Step 2: build the wiki

Check if `wiki/` already exists and is structured (index.md + topic folders).

- If it doesn't exist or is empty: invoke `wiki-compile` to build it from `wiki/raw/`. Tell the user what's happening: "Compiling now. This reads each doc, extracts entities (clients, projects, people, decisions, timeline), and writes a structured wiki under `wiki/`. Takes a few seconds."
- If it already exists: ask the user, "Looks like the wiki is already compiled. Want me to recompile from scratch so you see the build, or use the existing one?" Default to recompile if they say "either" or don't answer.

When the compile finishes, show the user what got generated:
- index.md
- counts per entity type (clients, projects, people, decisions, timeline)
- where each lives

## Step 3: query the wiki

Ask the user what they want to know. Offer suggestions tailored to the Foxglove workspace:

> The wiki covers 3 weeks of activity at Foxglove Co., a fictional 4-person B2B SaaS startup. What do you want to know? Some questions it answers well:
> - What did Foxglove ship for customers last month, what got dropped, and what's still open?
> - Why did we drop the Cresta deal?
> - Walk me through the Brightline incident: what happened, what we did, what we're changing.
> - What's the pricing story across these 3 weeks?
> - Who owns what at Foxglove right now and what are they working on?
>
> Or ask your own. The wiki shines on cross-doc questions where the answer lives across multiple meetings, memos, and daily logs.

Query the wiki (not `wiki/raw/`):

- Read `wiki/index.md` first
- Pick the topic pages relevant to the user's question (typically 3-5 pages)
- Load only those pages
- Synthesize an answer with specific cross-doc citations

Show the user the answer and note which topic pages you pulled from, so they see how the wiki's structure shapes the response. Briefly contrast with what they'd get if they queried `wiki/raw/` directly: a blurrier, more generic answer that costs more in tokens.

## Step 4: invite them to think about their own workspace

After the query lands, invite the user to think one step beyond the demo:

> What raw docs from your own workspace would this pattern work on? Past meeting notes, daily logs, project briefs, customer threads, internal memos. Any markdown directory you have. If you want to try it on your own docs today, drop them in `wiki/raw/` and re-run the compile (or jump to Stretch A in the build.md). You can also keep both this Foxglove demo and your own wiki side by side in different subdirectories.

## Step 5: stop

End with the feedback invitation:

> Anything you want me to change for next time? What worked, what didn't?

If the user replies with feedback, capture it as a dated free-form note (for future improvements to this flow).
