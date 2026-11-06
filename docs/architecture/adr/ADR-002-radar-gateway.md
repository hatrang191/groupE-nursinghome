# ADR-002: Radar data comes in through a Radar Gateway

| Field | Value |
|---|---|
| Status | Accepted |
| Date | Lesson 4 |

**Context.** The radar is an external device with its own data format. The alert starts from its
signals (UC-06), and the Admin needs to see whether each device is working (UC-13).

**Decision.** Add a Radar Gateway in the business layer. It receives the signals, checks them,
turns them into a simple event, and passes the event to the API Server. The API Server never
talks to the device directly.

**Consequences.** A change in the radar's data format only touches the gateway. The gateway
keeps the time of the last signal of each device, so the Admin can see a device that went
silent. The gateway is one more element to run and monitor.
