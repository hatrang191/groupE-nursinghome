# ADR-005: Send alerts as push notices to the web app on the phone

| Field | Value |
|---|---|
| Status | Accepted (after Lesson 7) |
| Date | Lesson 7 |

**Context.** The Nurse must see an alert within a minute, and the family must get a notice when
an alert is dangerous and when it is closed. Both groups carry a phone. The team does not build a
native app (ADR-001), and does not want a paid SMS service.

**Decision.** Send alerts as push notices to the web app installed on the phone. The API Server
asks the Notification Service, which sends the notice through the browser push service.

**Consequences.** Users must install the web app on the home screen and allow notices once; on
iPhone, web push only works for an installed web app. A push notice is not guaranteed to arrive,
so every alert is also shown inside the web app, and the Nurse must acknowledge it (the 1-minute
rule of ADR-004). The browser push service is a new external system and is added to the Context
Diagram.
