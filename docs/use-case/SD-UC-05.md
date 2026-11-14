# Sequence Diagram: SD-UC-05 Update condition

Analysis level: how the parts of the Dien Hong System talk to each other when a Nurse records a
resident's condition. The lifelines are the elements of the
[physical design](../architecture/architecture.md). The nurse's choices (which resident, which
condition, whether to correct and save again after a rejection) are shown in the activity diagram
[AD-UC-05](../activity/AD-UC-05.md), not here.

| Field | Value |
|---|---|
| Use case | UC-05 Update condition |
| Version | v1 |
| FRs | FR-02-01, FR-02-02, FR-02-03 |

```mermaid
sequenceDiagram
  actor NUR as Nurse
  participant W as Web App
  participant API as API Server
  participant DB as Database

  NUR->>W: Select resident, open Update resident condition (S-F02-02)
  W-->>NUR: Show condition form with the resident's name and room
  NUR->>W: Select or enter the condition and save
  W->>API: saveConditionUpdate(residentId, condition)
  API->>API: Check session token and role permission
  API->>API: Validate condition (FR-02-01)

  alt Condition is invalid (for example blank)
    API-->>W: Rejected: reason (FR-02-01)
    W-->>NUR: Show why the condition was rejected, keep the form for correction
  else Condition is valid
    API->>DB: Insert new Resident Condition Record [Saved] with time and nurse's name (FR-02-02)
    Note over API,DB: Append only: earlier records of the resident stay unchanged (FR-02-03)
    DB-->>API: Record saved
    API-->>W: Saved condition record
    W-->>NUR: Show the saved condition
  end
```

The `alt` fragment is an outcome decided inside the system (FR-02-01), so it belongs in a sequence
diagram. Correcting the condition and saving again is the nurse's choice (`a3` and the merge `m1`
in AD-UC-05): it repeats the exchange from "Select or enter the condition and save" and is not
drawn as a second fragment.

