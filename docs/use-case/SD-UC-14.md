# Sequence Diagram: SD-UC-14 View access and action logs

The Admin's choices (which user, which dates, filter again or stop) are not shown here.
The entries that the Admin reads are written by other use cases (sign-in, FR-06-13, and account and role changes, FR-06-08), not by this one.

| Field    | Value                                                            |
| -------- | ---------------------------------------------------------------- |
| Use case | UC-14 View access and action logs                                |
| Actor    | Admin                                                            |
| Version  | v1                                                               |
| FRs      | FR-06-03, FR-06-12 (the entries come from FR-06-08 and FR-06-13) |

```mermaid
sequenceDiagram
  actor A as Admin
  participant W as Web App
  participant S as API Server
  participant D as Database
  A->>W: open access and action logs
  W->>S: list log entries
  Note over S: every request: check session token and role-based permission
  break Admin has no permission to view the logs (FR-06-03)
    S-->>W: rejected: insufficient permission
    W-->>A: home screen access-denied message
  end
  S->>D: read access and action log entries
  D-->>S: entries (user, action, time)
  S-->>W: entries
  W-->>A: list of access and action entries (FR-06-12)
  A->>W: filter by user and date, apply
  W->>S: list log entries(user, date)
  break Admin has no permission to view the logs (FR-06-03)
    S-->>W: rejected: insufficient permission
    W-->>A: home screen with access-denied message
  end
  S->>D: read entries of the user and the date
  D-->>S: matching entries
  alt no entry matches the filter
    S-->>W: no entries
    W-->>A: empty list with a message that no entry matches
  else entries found
    S-->>W: matching entries
    W-->>A: filtered list of entries (FR-06-12)
  end
```

The `alt` is an outcome decided inside the system (entries found or not), so it belongs in a sequence diagram. The `break` is the system's refusal when the Admin's role has no permission to view the logs.
It appears twice because the API Server checks the permission on every request, so a role changed in UC-12 between the first request and the filter request is also respected.

Notes:

- UC-14 only reads. The Admin cannot change or delete log entries here, and no data is changed.
- The "no entry matches" branch is an assumption: FR-06-12 does not say what is shown for an empty result.
