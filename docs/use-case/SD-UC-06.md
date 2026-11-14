# Sequence Diagram: SD-UC-06 Send and acknowledge alert

Analysis level: how the parts of the Dien Hong System talk to each other from the moment the Radar
reports an abnormal event until a Nurse closes the alert, or until the failure is logged after the
third unacknowledged notice. The lifelines are the elements of the
[physical design](../architecture/architecture.md), including the Scheduler (ADR-004) and the
Browser push service (ADR-005). The nurse's actions (acknowledge, handle, report the outcome) are
shown in the activity diagram [AD-UC-06](../activity/AD-UC-06.md). The notices to the family are a
separate use case, [SD-UC-07](SD-UC-07.md).

| Field | Value |
|---|---|
| Use case | UC-06 Send and acknowledge alert |
| Version | v1 |
| FRs | FR-03-01 to FR-03-06; FR-03-08 (non-functional, see notes); FR-03-07 not drawn |

```mermaid
sequenceDiagram
  actor RAD as Radar
  participant GW as Radar Gateway
  participant API as API Server
  participant DB as Database
  participant SCH as Scheduler
  participant NS as Notification Service
  participant BP as Browser push service
  participant W as Web App
  actor NUR as Nurse

  RAD->>GW: Abnormal event (radar, room, event type, severity, time)
  GW->>GW: Check the event and turn it into a simple event (ADR-002)
  GW->>API: Abnormal event (radar, room, event type, severity, time)
  API->>DB: Read the resident in that room
  DB-->>API: Resident
  API->>DB: Save Alert [New] with resident, severity, location and time (FR-03-01, within 5 s)
  DB-->>API: Alert saved
  Note over API,DB: Alert [New] also starts the family side, SD-UC-07
  API-)W: Alert pop-up with resident, location and time on every signed-in nurse screen (FR-03-02)
  API->>NS: Push notice to the nurses on duty (FR-03-02)
  NS->>BP: Push notice
  BP-)W: Push notice on the nurse's phone
  W-->>NUR: Alert pop-up with sound, push notice
  API->>DB: Save notice count 1 and next check due in 1 min (ADR-004)

  par Wait and escalate (ADR-004)
    loop Until a nurse acknowledged or the failure is logged
      SCH->>DB: Find alerts whose next check is due
      DB-->>SCH: This alert
      SCH->>API: checkDueAlert(alertId)
      API->>DB: Read alert status and notice count
      DB-->>API: Status, notice count
      alt A nurse acknowledged
        API->>API: Send no more notices, the wait ends
      else No acknowledgement, fewer than 3 notices sent
        API->>DB: Set Alert [Escalated], add 1 to notice count, next check due in 1 min (FR-03-04)
        API-)W: Repeated pop-up on every signed-in nurse screen, notice n of 3 (FR-03-04)
        API->>NS: Push notice to the nurses on duty again (FR-03-04)
        NS->>BP: Push notice
        BP-)W: Push notice on the nurse's phone
        W-->>NUR: Not acknowledged yet (notice n of 3)
      else No acknowledgement, 3 notices sent
        API->>DB: Log failure of the alert, alert stays [Escalated], no more notices (FR-03-05)
      end
    end
  and Acknowledgement, at any moment after the first notice
    opt A nurse acknowledges the alert
      NUR->>W: Acknowledge (alert pop-up or Alert list S-F03-01)
      W->>API: acknowledgeAlert(alertId)
      API->>API: Check session token and role permission
      API->>DB: Set Alert [Acknowledged] (FR-03-03)
      DB-->>API: Alert saved
      API-)W: Alert no longer unattended on every signed-in nurse screen (FR-03-03)
      API-->>W: Alert acknowledged
      W-->>NUR: Show Alert [Acknowledged], stop the sound
    end
  end

  opt Alert is Acknowledged
    NUR->>W: Report the outcome, real event or false alarm (Close alert with outcome)
    W->>API: closeAlert(alertId, outcome)
    API->>API: Check session token and role permission
    API->>DB: Save the outcome, set Alert [Closed] (FR-03-06)
    DB-->>API: Alert saved
    API-->>W: Alert closed
    W-->>NUR: Show Alert [Closed]
    Note over API,DB: Alert [Closed] starts the closed notice of SD-UC-07
  end
```

The `alt` fragment inside the loop is decided by the system when the 1-minute wait ends (`d1` and
`d3` in AD-UC-06, same guards), so it belongs in a sequence diagram. The `par` fragment shows that
the two operands interleave: the Scheduler's rounds run on their own, and a nurse may acknowledge
during any round. Acknowledging and closing are the nurse's actions, so they appear as `opt`
(they may not happen: the failure end state has no acknowledgement), never as a system `alt`.

