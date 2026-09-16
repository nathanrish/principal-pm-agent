# Platform × Integration sync — 2026-09-11

Attendees: Platform lead, Integration lead, 2 engineers, QA

**Category mapping ownership (DEC-002)**
- Platform lead: mapping table should live in the integration service; Platform only exposes raw category codes.
- Integration lead: Platform should own the canonical category model; otherwise every consumer re-implements mapping.
- No agreement. **Agreed to align next week.**
- Meanwhile Integration hardcoded mappings for CAT-1 and CAT-2 in the integration service "to keep moving". Will need to be replaced once the model is decided.

**Partner webhook v2**
- Partner contact said "end of month" on a call. Nothing in writing. We need it for CAT-6 to CAT-9 invoice events.

**Other**
- QA flagged no test data yet for CAT-3–CAT-9.
