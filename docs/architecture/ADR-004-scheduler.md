# ADR-004: Add a scheduler for the alert wait

| Field | Value |
|---|---|
| Status | Accepted (after Lesson 7) |
| Date | Lesson 7 |

**Context.** The alert rule says: when a Nurse does not acknowledge an alert within 1 minute,
notify again; after the 3rd notice without acknowledgement, record a failure. The activity
diagram of UC-06 shows a time event that starts by itself, with no user action. The Lesson 4
architecture had no element that can start a step by time.

**Decision.** Add a Scheduler in the business layer. When the API Server sends a notice, it
saves the time the next check is due on the alert in the database. The Scheduler looks for alerts
that are due and wakes the API Server, which escalates or records the failure.

**Consequences.** The wait is stored in the database, so a restart does not lose a running alert.
The wait length (1 minute) and the maximum number of notices (3) are fixed in the system settings
in this release; the Admin cannot change them. Timed behaviour needs its own use case (see the Inputs check in
[activity-index](../activity/activity-index.md)).
