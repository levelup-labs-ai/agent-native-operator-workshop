# Use sub-agents for a research task

Help the user run a research task using sub-agents at a smaller model tier. Sub-agents fan out work in parallel: each runs in its own session with a smaller context (no SOUL/USER/MEMORY) and a cheaper model. The main agent synthesizes their results into one answer.

The workshop ships two sub-agent prompt templates under `agents/`. You'll use the relevant template text when spawning isolated sub-sessions with `sessions_spawn`:

- **`doc_reader`** (`agents/doc_reader/AGENT.md`) — reads ONE markdown document at a given path and returns a structured ~200-word summary. Handles workspace docs (meeting notes, daily logs, memos, proposals, order forms, task snapshots) and research papers. Use this when the user has a folder of markdown to fan out over.
- **`research_worker`** (`agents/research_worker/AGENT.md`) — runs web searches on ONE specific angle of a topic and returns a distilled brief. Use this when the user wants open-web research on a topic.

Do not rely on the templates being registered as callable named agents. For this worksheet, always spawn isolated sub-sessions and pass the smaller worker model explicitly through `sessions_spawn.model`.

Model-routing rule: `agents.defaults.models` is the configured model catalog/allowlist, not the sub-agent default. Adding the smaller model there (`openai/gpt-5.4-mini` or `openai-codex/gpt-5.4-mini`, depending on the user's OpenAI auth route — see Step 1) only makes it available to OpenClaw. The actual smaller-model routing for this worksheet comes from the explicit `sessions_spawn.model` value on each child session.

## Step 1: choose the smaller model, then finish installing the sub-agents

(a) Choose the smaller worker model from the user's current default and, for OpenAI, their auth route:

- If the current default model is an Anthropic model (`anthropic/...`), use `anthropic/claude-haiku-4-5`. Skip the OpenAI setup in (b).
- If the current default model is an OpenAI model, the smaller model depends on **how the user's OpenAI provider authenticates**, because OpenAI exposes the same small model on two different auth paths:
  - **Platform API key** (a key set on the `openai` provider) → use `openai/gpt-5.4-mini`.
  - **ChatGPT subscription / OAuth (Codex route)** → use `openai-codex/gpt-5.4-mini`.

Call this value `smaller_model` for the rest of the worksheet.

Pick the route that matches the user's working auth, not the default model's prefix. The prefix is not a reliable signal — a default of `openai/gpt-5.5` can still have working auth only on the `openai-codex` route. To tell which route is live, read the auth overview in `openclaw models status` and note which provider prefix the authenticated/working models use. If you can't tell from status, treat the one-shot verify in (c) as the decider: a call that fails with `insufficient permissions, missing api.responses.write` means you picked the wrong route (or the OAuth token lacks scope — see (c)).

Note for later: if `smaller_model` is `openai-codex/gpt-5.4-mini`, it bills against the ChatGPT subscription, not per token. The Step 5 cost table may therefore show $0 or blank token cost for those child sessions — that is expected, not a bug. For a real per-token cost line, the API-key `openai/gpt-5.4-mini` route is needed instead.

If `smaller_model` is `anthropic/claude-haiku-4-5`, skip the OpenAI setup below. That model is already configured in the workshop setup; do not add a new Anthropic model.

(b) OpenAI-only setup: if `smaller_model` is an OpenAI model (`openai/gpt-5.4-mini` or `openai-codex/gpt-5.4-mini`), check the user's OpenClaw models with the actual models CLI. Substitute `smaller_model` for the model string throughout, and use the matching `--provider` (`openai` for `openai/...`, `openai-codex` for `openai-codex/...`).

Important: a model being listed by `openclaw models list` only means it is available (in the catalog OpenClaw can reach). It does not mean it is **configured** in the `agents.defaults.models` allowlist, which is what governs `sessions_spawn.model` overrides. Check the configured allowlist with `openclaw models status`.

1. Run `openclaw models list --all --provider <provider> --plain` and confirm `smaller_model` appears in the full catalog.
2. Run `openclaw models status` and check whether `smaller_model` appears on the `Configured models` line. That line is the `agents.defaults.models` allowlist.
3. If `smaller_model` is in the full catalog but not on the `Configured models` line, add it with the OpenClaw config CLI. Do not hand-edit `openclaw.json` for this step.

   First dry-run (substitute `smaller_model`):

   ```bash
   openclaw config set agents.defaults.models '{"<smaller_model>":{}}' --strict-json --merge --dry-run
   ```

   If the dry-run shows only the intended additive change, apply it:

   ```bash
   openclaw config set agents.defaults.models '{"<smaller_model>":{}}' --strict-json --merge
   openclaw config validate
   ```

   If the command says the gateway must restart before the change applies, restart or reload OpenClaw, then run `openclaw models status` again and confirm `smaller_model` now appears on the `Configured models` line.

(c) Verify the smaller model is usable before continuing:

1. Run a one-shot test sub-session with `sessions_spawn.model` set to `smaller_model`.
2. Confirm the child session reports `smaller_model`, not the parent model. Check the child rollout/log/session metadata, not just the parent agent's prose.
3. If the child inherits the parent model, stop the worksheet run. Re-check that the smaller model is on the `Configured models` line of `openclaw models status`, restart or reload OpenClaw if config changed, and rerun the one-shot test. If it still inherits the parent after that, report a model override failure instead of claiming the cheaper route worked.
4. If the child fails with `insufficient permissions, missing api.responses.write` (or another `missing scopes` / auth error), this is the OpenAI auth route, not the model config. Do **not** re-run `config set`. Instead:
   - You picked the wrong route in (a): switch `smaller_model` to the other variant (`openai/gpt-5.4-mini` ⇄ `openai-codex/gpt-5.4-mini`), make sure it is configured per (b), and rerun the test.
   - Or the auth itself is short on scope: for the OAuth/Codex route, re-authenticate the OpenAI login — forcing a fresh consent (revoke OpenClaw's authorization in the OpenAI/ChatGPT account first), since a plain re-login can replay the old scopes. For the API-key route, ensure the key grants Responses-API write (`api.responses.write`) in the OpenAI dashboard, or use a full-access key. (Background on this scope issue is in `troubleshooting.md`.)
   Only after a clean one-shot run (correct recorded model, no auth error) treat the cheaper route as working.

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
- After spawning, verify the child sessions actually used `smaller_model` by checking each child's rollout/log/session metadata.
- Wait for all of them to come back. If the parent chat does not render the final synthesis even though child runs are terminal/delivered, inspect `/subagents list`, `/subagents info`, `/subagents log`, or the session metadata/results directly, then synthesize from the child outputs.
- Synthesize into the final answer.
- Show the answer to the user.

If the child sessions inherit the parent model instead of `smaller_model`, tell the user plainly: The fan-out still works but the cost win did not apply. Confirm `smaller_model` is on the `Configured models` line of `openclaw models status`, restart/reload state, and the explicit `sessions_spawn.model` value before treating it as an OpenClaw runtime issue.

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
