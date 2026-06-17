# Diego — Fri May 1, 2026

## Today

Credit policy memo published. Brightline postmortem sent at 14:30. Q2 closes clean.

Credit policy memo (D4):
- Three tiers (outage, onboarding delay, feature failure)
- Credits scale to outage length, applied automatically when threshold hits
- New order forms reference the memo by date (May 1, 2026)
- Brightline's $4K credit is consistent with Tier 1 (5+ days outage)

Brightline postmortem went to Sarah today. Four pages. What went wrong (schema discovery missed the custom SF object pattern), what we did about it (mapping rewrite, $4K credit, accelerated go-live), what we're changing (schema review checklist will include explicit non-standard object pattern probes for all future onboardings). Sarah replied at 15:10 thanking us and confirming the issue is resolved on their end.

Acme contract signed Tuesday. Order form v2 is the first to reference the new credit policy by date. Clean handoff.

## Meetings

- 9:00 Maya: credit policy final edit + sign-off
- 11:00 Policy memo publishes (Slack + customer portal)
- 14:00 Postmortem doc final, send to Sarah
- 16:00 Weekly retro (whole team)

## Decisions

- D4 closed: Credit policy memo formalized and published.

## Action items

- Onboarding schema review checklist update: draft next week
- Customer-facing 1-pager: Priya owns design, starts Monday May 4
- Renewal playbook draft: starts Monday

## Notes

Quarter ends with all four decisions resolved. D1 (drop Cresta) and D2 (Acme expansion) settled in week 2. D3 (Q3 pricing) decided week 1 and shows up in every customer conversation. D4 (credit policy) the messiest, but closes today. The wiki page for `decisions/` will basically write itself from these four threads.
