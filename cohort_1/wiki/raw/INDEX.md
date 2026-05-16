# Foxglove Co. — 3 weeks of workspace (Apr 13 to May 1, 2026)

This directory holds the source documents for the wiki-layer demo. The docs are a synthetic snapshot of one small startup's workspace over three weeks. Drop them into your own workspace, run `wiki-compile`, and you get a structured wiki you can query.

## About Foxglove

Foxglove Co. is a 4-person B2B SaaS startup. Their product is an AI sales-call summarizer for RevOps teams at mid-market SaaS companies. It pulls call recordings from Gong / Chorus, summarizes them, and syncs the summary plus coaching flags into the CRM. The company is ~14 months old, seed-stage, with ~6 paying customers and a few deals in flight.

## Members

- **Maya Chen** — CEO / founder. Runs sales, fundraising, and product calls.
- **Diego Park** — Head of Customer Success. Owns onboarding, renewals, and support.
- **Priya Nair** — Design + Marketing. Brand, website, customer comms.
- **Tomás Reyes** — Engineering lead. Builds the product.

## Customers in this window

- **Acme Ironworks** — live customer, mid-expansion. Going from Starter to Growth tier. +$18K ARR if it lands.
- **Brightline Health** — mid-onboarding. CRM integration broke in week 2. Credit + refund conversation triggered.
- **Cresta Studio** — prospect. Pushed back on Q3 pricing. Foxglove walked away in week 2.
- **Dovetail Energy** — new lead. Discovery W1, proposal W2, asked for changes W3.

## Doc inventory

- **Daily logs (10):** `daily-<name>-YYYY-MM-DD.md`. Maya 5, Diego 3, Priya 1, Tomás 1.
- **Meeting notes (6):** `meeting-<topic>-YYYY-MM-DD.md`. Granola-transcript style.
- **Sales docs (3):** `order-form-...md`, `proposal-...md`.
- **Internal memos (3):** `memo-<topic>-YYYY-MM-DD.md`.
- **Task snapshots (2):** `tasks-week-N-YYYY-MM-DD.md`. Linear-style boards.

## Decisions seeded across multiple docs

These are the cross-doc threads a good wiki compile should surface as `wiki/decisions/`:

- **D1: Drop Cresta deal.** Maya, Apr 20.
- **D2: Greenlight Acme expansion (+$18K ARR).** Maya + Tomás, Apr 21.
- **D3: Bump list price 20% for new logos starting Q3.** Maya, Apr 13.
- **D4: Brightline refund + new credit policy.** Diego + Maya, Apr 22 to May 1.

## Why this set earns its keep

The query the wiki has to answer well is something like:

> What did Foxglove ship for customers last month, what got dropped, and what's still open?

A well-compiled wiki produces:
- `clients/acme-ironworks.md`, `clients/brightline-health.md`, etc.
- `projects/acme-expansion.md`, `projects/brightline-onboarding.md`
- `people/maya-chen.md`, `people/diego-park.md`, etc.
- `decisions/drop-cresta.md`, `decisions/acme-expansion.md`, `decisions/q3-pricing.md`, `decisions/brightline-credit-policy.md`
- `timeline/week-1.md`, `timeline/week-2.md`, `timeline/week-3.md`

Querying the wiki pulls index + 3 to 5 topic pages. Querying the raw set loads all 24 docs.

The wiki version is also more grounded because it has explicit decisions and timeline pages. The agent can construct a real narrative instead of blurring all 24 docs together.
