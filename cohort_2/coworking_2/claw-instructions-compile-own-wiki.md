# Compile a wiki from the user's own documents

The user brings documents they actually have — past project docs, research, meeting notes, writing samples. Help them seed a knowledge wiki from their real material.

## Step 1: check for wiki-compile skill

Confirm `<workspace>/skills/wiki-compile/SKILL.md` exists. If missing, walk the user through installing it before continuing.

## Step 2: ask what documents they want to seed with

Ask the user, conversationally:

"What documents do you want to seed your wiki with? Some ideas:
- Past project docs or design briefs
- Research papers or articles you've collected
- Meeting transcripts (from Granola if connected)
- Past writing samples — newsletters, blog posts, internal memos
- Saved threads from Slack or email

Bring 5 to 30 documents. More is fine but not necessary. They should share a topic or theme so the wiki has structure."

Wait for the user's answer. If they're not sure, suggest: "If unsure, start with everything in one project folder you've been working on lately. Compile, query, see if it's useful."

## Step 3: get the documents into wiki/raw/

Based on what the user has:

- **Documents already in their workspace**: ask them to copy or move them to `wiki/raw/`. Help with paths.
- **Documents in Google Drive**: the Composio MCP is already wired from Coworking 1. Check whether the user has Google Drive connected in Composio (run a quick list call). If not, tell them:

  > Go to **app.composio.dev**, search for **Google Drive**, click Connect, and complete the OAuth flow. Come back when it shows as active.

  Once connected, use the Composio Google Drive tools to search for and fetch the files they want. Save each to `wiki/raw/`. Show them the file names as they land.

- **Documents in email or Slack**: that's harder to pull programmatically. Suggest exporting to markdown or plain text first, then dropping the files into `wiki/raw/` manually.

Wait for them to confirm the documents are in `wiki/raw/`. Show them the file count.

## Step 4: run wiki-compile

Tell the user: "I'm going to run wiki-compile. It reads each document, extracts entities (methods, people, claims, timeline), and writes structured pages to wiki/. Takes ~30 seconds per 5 documents at Sonnet."

Run wiki-compile. Show progress as it goes. When done:

- Show the user the resulting `wiki/` structure (file tree)
- Open `wiki/index.md` and read it back so they see what got compiled
- Capture session-cost cost for the compile

## Step 5: query their wiki

Ask the user: "What's a question you've actually been wondering about your own knowledge? Something you'd want a grounded answer to with citations?"

Wait for their question. Run it against the wiki:

> [User's question]. Use wiki/ to answer. Start with wiki/index.md and load only the topic pages you actually need. Cite specific source documents.

Show them the answer + session-cost for the query.

## Step 6: stop

Stop when the user has seen their wiki produce a real answer. Tell them:

"This wiki is yours now. Add documents to wiki/raw/ over time and re-run wiki-compile to fold them in. The wiki gets smarter as you add content. Run /skill-review when you want to review feedback patterns — it can propose recompiling if wiki/raw/ has new files."

Ask for any reactions or notes. Append to `skills/wiki-compile/feedback.md`.
