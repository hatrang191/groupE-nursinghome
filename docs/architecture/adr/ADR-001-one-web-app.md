# ADR-001: One web app for every user

| Field | Value |
|---|---|
| Status | Accepted |
| Date | Lesson 4 (updated Lesson 7) |

**Context.** Five groups use the system: Receptionist, Nurse, Residents' Family, BOD and Admin.
Families and Nurses need alerts on their phones; the other roles mostly work on a computer.
Push notices (ADR-005) need a web app that can be installed on the phone.

**Decision.** Build one responsive web app for all users, installable on the phone home screen.
No native mobile app in this release.

**Consequences.** One code base fits the team capacity (4 people, 240 person-hours). Screens
adapt to phone and computer. A native app is out of scope for this release. Families must install
the web app on the home screen once to receive push notices (see ADR-005).
