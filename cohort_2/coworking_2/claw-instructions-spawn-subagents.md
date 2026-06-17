# Use sub-agents for a research task

Help the user run a research task using sub-agents at a smaller model tier. Sub-agents fan out work in parallel: each runs in its own session with a smaller context (no SOUL/USER/MEMORY) and a cheaper model. The main agent synthesizes their results into one answer.

The workshop ships two sub-agent prompt templates under `agents/`. You'll use the relevant template text when spawning isolated sub-sessions with `sessions_spawn`:

- **`doc_reader`** (`agents/doc_reader/AGENT.md`) — reads ONE markdown document at a given path and returns a structured ~200-word summary. Handles workspace docs (meeting notes, daily logs, memos, proposals, order forms, task snapshots) and research papers. Use this when the user has a folder of markdown to fan out over.
- **`research_worker`** (`agents/research_worker/AGENT.md`) — runs web searches on ONE specific angle of a topic and returns a distilled brief. Use this when the user wants open-web research on a topic.

Do not rely on the templates being registered as callable named agents. For this worksheet, always spawn isolated sub-sessions and pass the smaller worker model explicitly through `sessions_spawn.model`.

## Step 1: choose the smaller model, then finish installing the sub-agents

(a) Choose the smaller worker model from the user's current default:

- If the current default model is an OpenAI GPT model (`openai/gpt...`), use `openai/gpt-5.4-mini`.
- If the current default model is an Anthropic model (`anthropic/...`), use `anthropic/claude-haiku-4-5`.

Call this value `smaller_model` for the rest of the worksheet.

If `smaller_model` is `anthropic/claude-haiku-4-5`, skip the OpenAI setup below. That model is already configured in the workshop setup; do not add a new Anthropic model.

(b) OpenAI-only setup: if `smaller_model` is `openai/gpt-5.4-mini`, check the user's OpenClaw models with the actual models CLI.

1. Run `openclaw models list --all --provider openai --plain` and confirm `openai/gpt-5.4-mini` appears in the full catalog.
2. Run `openclaw models list --plain` and confirm `openai/gpt-5.4-mini` appears in the configured models. OpenClaw shows configured models by default from `agents.defaults.models`, which is the allowlist/catalog of models OpenClaw can use.
3. If `openai/gpt-5.4-mini` appears in the full catalog but not in the configured models, add it to `agents.defaults.models`.

(c) Verify the smaller model is usable before continuing:

1. Run a one-shot test sub-session with `sessions_spawn.model` set to `smaller_model`.
2. Confirm the child session reports `smaller_model`, not the parent model.

(d) Open the relevant prompt template before spawning:

- For markdown/file research, read `agents/doc_reader/AGENT.md`.
- For open-web research, read `agents/research_worker/AGENT.md`.

Use the template's task instructions in the spawned sub-session prompt. Do not edit the `AGENT.md` files just to set the model; the model is passed explicitly through `sessions_spawn.model`.

## Step 2: ask what to research, route to the right sub-agent

Ask the user what they want to research. Offer options:

> What do you want to research? Pick one:
> 1. The Foxglove Co. workspace shipped in `wiki/raw/` — 24 docs spanning 3 weeks of a synthetic startup's daily logs, meeting notes, memos, sales docs, and task snapshots. Good prompts to explore: "summarize what happened at Foxglove across these 3 weeks," "what were the key decisions and what drove them?", or "what's the story arc with each customer?" — I'll use the `doc_reader` template to fan out across the docs.
> 2. Your own markdown folder — point me at a directory of `.md` files and a question. I'll use the `doc_reader` template to fan out across the files.
> 3. An open-web topic — give me a question and 3-5 angles to research. I'll use the `research_worker` template to fan out across the angles.

Wait for the user's answer. Then:
- Option 1 or 2 → use the `doc_reader` template. The fan-out unit is "one file per sub-session" (or a small bucket of files per sub-session if there are many).
- Option 3 → use the `research_worker` template. The fan-out unit is "one angle per sub-session." If the user only gave a topic without angles, propose 3-5 angles and get approval before spawning.

## Step 3: plan the spawn

Design the fan-out in plain English:

- Which template (`doc_reader` or `research_worker`)
- How many sub-sessions (typically 3-5 for parallel work)
- What each one gets:
  - `doc_reader`: one file path (or a small bucket of paths) per sub-session
  - `research_worker`: one angle string per sub-session
- How the main agent synthesizes (one coherent answer, ~400 words)

Show the plan to the user. Wait for approval before firing.

## Step 4: run the sub-agents

On approval:

- Spawn isolated sub-sessions in parallel with `sessions_spawn`.
- For each spawned sub-session, set `model` / `sessions_spawn.model` to `smaller_model`.
- Put the relevant template instructions (`doc_reader` or `research_worker`) plus the assigned file path, bucket of paths, or research angle directly in that sub-session's task prompt.
- After spawning, verify the child sessions actually used `smaller_model` by checking the log file for each session.
- Wait for all of them to come back.
- Synthesize into the final answer.
- Show the answer to the user.

If the child sessions inherit the parent model instead of `smaller_model`, tell the user plainly: The fan-out still works but the cost win did not apply. The failure is likely a harness/runtime issue.

## Step 5: summarize, show cost, and stop

After the research answer is shown, compute this run's session cost.

Use the `session-cost` skill. If it is not installed, install `session-cost` from ClawHub first, then use it for this run.

Show the user a compact Markdown table with these columns:

| Agent/session | Role | Model | Input tokens | Output tokens | Cost |
|---|---|---|---:|---:|---:|

Only focus on the current session and the sub-sessions spawned associated with this.

Then end with:

- The synthesized research answer.
- The cost table.

End with the feedback invitation:

> Anything you want me to change for next time? What worked, what didn't?

If the user replies with feedback, capture it as a dated free-form note for future improvements to this flow.
