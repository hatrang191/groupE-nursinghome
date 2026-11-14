# Sequence Diagram: SD-UC-13 Monitor radar status

The Admin only requests and reads the status, so there is no Admin choice to show here; the three endings (rejected, status unavailable, status shown) are in the activity diagram [AD-UC-13](../activity/AD-UC-13.md).

| Field | Value |
|---|---|
| Use case | UC-13 Monitor radar status |
| Actor | Admin |
| Version | v1 (aligned with AD-UC-13 v0.1) |
| FRs | FR-06-03, FR-06-09, FR-06-10, FR-06-11 |

```mermaid
sequenceDiagram
  actor A as Admin
  participant W as Web App
  participant S as API Server
  participant D as Database
  participant G as Radar Gateway
  A->>W: request radar status
  W->>S: get radar status
  Note over S: every request: check session token and role-based permission
  break Admin has no permission to monitor radar status (FR-06-03)
    S-->>W: rejected: insufficient permission
    W-->>A: home screen of the role with access-denied message
  end
  S->>D: read registered radars
  D-->>S: registered radars
  alt no radar is registered (FR-06-11)
    S-->>W: status unavailable: no radar registered
    W-->>A: message that the status cannot be shown, no old status
  else at least one radar is registered
    S->>G: get time of last successful check and last data of each registered radar
    alt Radar Gateway cannot be reached or does not answer (FR-06-11)
      S-->>W: status unavailable: no answer from the Radar Gateway, no stored status used
      W-->>A: message that the status cannot be shown, no old status
    else Radar Gateway answers
      G-->>S: last successful check and last data of each radar
      S->>S: set each radar Offline if no response for more than 5 minutes, otherwise Online (FR-06-09)
      S-->>W: every registered radar with status and time of last data
      W-->>A: show each radar with its status and time of last data (FR-06-09)
      opt at least one radar is Offline (FR-06-10)
        W-->>A: highlight the Offline radars
      end
    end
  end
```

The `alt` fragments here are outcomes decided inside the system (no radar registered, Radar Gateway not reachable, radar Offline or not), so they belong in a sequence diagram. The `break` is the system's refusal when the Admin's role has no permission (D1 [No]). The API Server asks the Radar Gateway for the status every time the Admin requests it and keeps no copy, so an old status is never shown as the current one (FR-06-11).

Notes:

- UC-13 only reads. No data is changed and no action log entry is written by this diagram.
- A radar only sends abnormal events (UC-06), so a quiet radar may have no recent data. For this reason Online or Offline is worked out from the time of the last successful check or data, whichever is later, and the time of the last data is only shown to the Admin.
- The 5-minute limit comes from the W1 row FR-06-09-BE and is still an assumption.
- The highlight is drawn by the Web App (FR-06-10-FE) from the status the API Server sends.
- "No radar registered" and "Radar Gateway cannot be reached" end with the same message, so they appear as two branches with the same last message.
