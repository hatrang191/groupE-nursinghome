# ADR-002: Radar data comes in through a Radar Gateway

| Field | Value |
|---|---|
| Status | Accepted |
| Date | Lesson 4 |

**Context.** The radar (RETINA-4SN) is an external device with its own data format. Its built-in
AI decides when an event is abnormal and gives it a severity, and the radar only sends a message
when that happens (UC-06). The Admin also needs to see whether each radar is working (UC-13). A
radar in a quiet room sends nothing, so silence alone cannot show whether a radar is healthy or
broken: a radar that is unplugged, frozen or off the Wi-Fi looks the same as one in a room where
nothing happens, and a fall in that room would raise no alert at all.

**Decision.** Add a Radar Gateway in the business layer. It is the only element that talks to the
radars, and it has two jobs:
1. **Receive abnormal events.** The gateway receives each abnormal event from a radar, checks it,
   turns it into a simple event (radar, room, event type, severity, time) and passes it to the API
   Server, which creates the alert (FR-03-01).
2. **Check radar connections.** The gateway checks the connection of every registered radar at a
   fixed interval and keeps, for each radar, the time of the last successful check and of the last
   event (FR-06-09-INT).
The API Server never talks to a radar directly. It decides each radar's status from the times the
gateway keeps: a radar is Offline when it has not answered a check for 5 minutes, otherwise Online
(FR-06-09-BE). If the gateway itself cannot be reached, the API Server reports that the radar
status cannot be shown and never shows an old status as the current one (FR-06-11).
   
**Consequences.** 
- A change in the radar's data format or radar model only touches the gateway.
- The Admin can see a broken radar even when no abnormal event has arrived, and can fix it before
  a fall goes unnoticed (UC-13).
- The connection checks add a small, regular load on the nursing home's network. The check
  interval is a trade-off: a shorter interval finds a broken radar sooner but sends more checks.
- The gateway is one more element to run and monitor. If it stops, no alerts and no radar status
  reach the API Server, so it falls under the "always on" requirement and restarts automatically
  after a failure (FR-03-08).
