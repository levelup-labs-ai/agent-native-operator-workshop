---
name: wiki-compile
description: Compile a structured knowledge wiki from raw markdown documents. Reads documents in wiki/raw/, extracts entities (methods, people, claims, timeline events), generates an index plus topic pages in wiki/, and links everything together. Designed for the Karpathy LLM Wiki pattern. Invoke when user says "compile my wiki", "build a wiki from these documents", or "recompile wiki/".
---

## My setup

This section gets customized at install time. The skill body reads
from these fields. If placeholder text (`<...>`) is still present,
ask the user to seed it before running.

### Source directory
<default: wiki/raw/. The user can point at a different folder.>

### Output directory
<default: wiki/. Sibling to raw/. The user can override.>

### Entity types to extract
<default: methods, people, claims, timeline. The user can add or
remove based on the source material — for client meetings it'd be
people + projects + decisions; for product docs it'd be features +
specs + bugs.>

### Per-page word cap
<default: 400 per topic page; 1000 for index.md. The user can
override based on how dense the source material is.>

---

# Wiki Compile

You compile a structured knowledge wiki from raw markdown documents.
Pattern: Karpathy's LLM Wiki — `raw/` is source, you are the
compiler, `wiki/` is the executable output queries pull from.

## Inputs

- `wiki/raw/` (or the source directory from "My setup") — the raw documents to compile
- The "My setup" section above (entity types, output dir, word caps)
- Existing `wiki/index.md` if any (so you can detect what's already compiled and avoid duplicate work)
- `skills/wiki-compile/feedback.md` — recent reactions; honor them

## Process

1. Scan all markdown files in the source directory. For each one, extract title, date/year, authors, main argument, methods/techniques introduced, people mentioned, key findings. Keep a running list of all entities.
2. Cluster references to the same entity across documents (one page per entity, not per mention).
3. Generate topic pages under `wiki/<entity_type>/<name>.md` for each entity type in "My setup." Cross-link via `[[wiki-links]]`. Honor the per-page word cap.
4. Generate `wiki/timeline/<year>.md` pages if "timeline" is in the entity types list.
5. Write `wiki/index.md` (~1000 words) as the always-load page: brief overview, section per entity type with one-line descriptions, source list, timestamp.
6. Tell the user what was compiled (counts per entity type, total tokens, compile cost, an example query).

End every compile/query confirmation with:

> Anything you want me to change for next time? What worked, what didn't?

When the user replies with feedback, append a dated free-form note to `skills/wiki-compile/feedback.md` and confirm: "Got it. I'll remember this for next time."

## Hard rules

- Never invent entities, claims, or findings not present in the
  source documents
- Never overwrite existing wiki pages without showing a diff first
  if they have content beyond a placeholder
- Never exceed the per-page word cap
- Each entity lives in ONE place (one file). Reference it via
  wiki-links from other pages, don't duplicate.
- The index always loads first; topic pages load on demand. Keep
  the index small (~1000 words).

## When the compile fails

If a source document is malformed or unreadable:

- Skip it, log it to `skills/wiki-compile/failures/<filename>.md`
  with the reason
- Continue with the rest

If the source directory is empty:

- Tell the user: "wiki/raw/ is empty. Drop documents there first,
  then re-run /wiki-compile."

## Recompiling

When `wiki/raw/` gets new documents:

- Detect via comparing filenames against what was processed last
  run (the index has a timestamp)
- Recompile only the affected entity pages, not the whole wiki
- Update the index with new entries

If the user says "rebuild the wiki from scratch," delete `wiki/`
(except `raw/`) first, then full compile.

---

## What's OpenClaw native vs workshop convention

| Mechanism | Source |
|---|---|
| File-based wiki structure | Karpathy LLM Wiki pattern (April 2026) |
| Index + topic pages + on-demand loading | OpenClaw native (progressive disclosure) |
| `MEMORY.md` (not used here — wiki is its own thing) | OpenClaw native |
| Inline "My setup" in SKILL.md | OpenClaw native pattern |
| `skills/wiki-compile/feedback.md` | **Workshop addition** |
| `skills/wiki-compile/failures/` | **Workshop addition** |
