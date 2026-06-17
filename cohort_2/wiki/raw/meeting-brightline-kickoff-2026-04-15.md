# Brightline Health — Onboarding kickoff

Date: Wed Apr 15, 2026, 13:00 to 13:50 PT
Attendees: Diego Park (Foxglove), Tomás Reyes (Foxglove), Sarah Okonkwo (Brightline, Head of Sales Ops), Mike Brennan (Brightline, SF admin)

## Agenda

- Walk through the 4-week onboarding plan
- Confirm CRM integration scope
- Set expectations for go-live

## Discussion

**Diego:** Welcome. Brightline is on Starter at 8 seats, signed last week. We're shooting for full go-live by Apr 30. Tomás runs the integration build.

**Sarah:** Just to set context: my AEs are in active sales motion right now. We can't have a long ramp. Two weeks for the integration is the ceiling.

**Tomás:** Two weeks is doable for the standard Salesforce nightly sync. Real-time would be Growth tier and longer to build, but you're not asking for that.

**Mike:** We use a custom SF setup. Two custom objects for call notes and one for opportunity coaching. The mapping needs to hit those, not the standard call object.

**Tomás:** That's where I'd want to look at your SF schema before committing. Can you grant me a sandbox login this week?

**Mike:** Yes, I'll send credentials today.

**Diego:** Standard coaching taxonomy work for you on the AE side?

**Sarah:** I think so for v1. We can revisit in a month.

**Diego:** Good. So the plan: Week 1 (this week) is access setup and schema review. Week 2 is the build. Week 3 is testing with the AEs. Week 4 go-live.

**Sarah:** What if the schema review reveals something messier than expected?

**Tomás:** I'll flag it the day I see it. We have buffer in week 3 testing.

## Decisions

- Onboarding go-live target: Apr 30, 2026.
- Integration scope: nightly Salesforce sync to Brightline's custom objects (call notes, opportunity coaching).
- Standard coaching-flag taxonomy for v1; revisit in 30 days.

## Action items

- Mike: send SF sandbox credentials to Tomás by EOD Apr 15
- Tomás: schema review (Apr 16 to 17), flag any blockers immediately
- Diego: schedule AE training session for week 3
- Sarah: pick 3 AEs for week 3 pilot

## Open questions

- Brightline's SF custom objects could complicate the mapping. Real risk; Tomás will know by end of week.
