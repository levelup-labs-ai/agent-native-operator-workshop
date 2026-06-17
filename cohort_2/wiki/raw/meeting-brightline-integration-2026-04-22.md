# Brightline Health — Integration crisis call

Date: Wed Apr 22, 2026, 09:30 to 10:20 PT
Attendees: Diego Park (Foxglove), Tomás Reyes (Foxglove), Sarah Okonkwo (Brightline), Mike Brennan (Brightline, SF admin)

## Context

The integration build hit a wall yesterday. Brightline's custom Salesforce objects use a non-standard relationship pattern (call notes are children of opportunities, not accounts). The nightly sync is writing to the wrong parent record and Sarah's team is getting empty summaries on opportunities.

## Discussion

**Sarah:** This is bad. My AEs were ready to go live next week. Right now they're seeing blanks where the call notes should be.

**Tomás:** Yeah. The schema review on Apr 17 missed it. The custom object relationship isn't documented in the standard SF metadata; I had to find it by hand reading record links yesterday. That's on me.

**Mike:** It's also on us. We didn't flag the non-standard pattern when we set up the sandbox.

**Tomás:** Fix is real. Two days of engineering to rewrite the mapping logic. Plus a day of regression testing.

**Diego:** Sarah, what do you need from us to keep your team's confidence?

**Sarah:** I need a real timeline and I need to know what you're going to do for us. We can't ship Apr 30 like we planned.

**Tomás:** New go-live: Apr 29. The mapping rewrite is two days max. I'm pulling in tomorrow.

**Diego:** And on the credit side: this is on us, not you. I'm applying a 2-month service credit, $4,000 against your annual, effective on your next invoice. Brightline doesn't need to ask.

**Sarah:** That's the right answer. Appreciate that you're not making me argue for it.

**Diego:** Standard policy. Or it will be soon; we're formalizing the credit policy this quarter because of this.

**Sarah:** Send me a written postmortem by end of next week.

**Tomás:** You'll have it.

## Decisions

- Mapping rewrite to land Apr 28; regression Apr 29. Go-live moved to Apr 29.
- Service credit applied to Brightline: $4,000 (2 months on Starter at $2K MRR).
- Credit applied automatically. No customer-side action needed.
- Postmortem to Brightline by May 1.

## Action items

- Tomás: ship mapping rewrite by Apr 28
- Tomás: regression test Apr 29 morning
- Diego: apply $4K credit to Brightline's invoice line item (this week)
- Diego: write postmortem document, send by May 1
- Diego + Maya: formalize credit policy as a written memo this quarter

## Open questions

- Are there other non-standard SF patterns we should pressure-test for other onboardings?
