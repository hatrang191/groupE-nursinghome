# Sequence Diagram: SD-UC-08 View resident's condition

| Field | Value |
|---|---|
| Use case | UC-08 View resident's condition |
| Version | v1 |
| FRs | FR-04-01, FR-04-02 |

Analysis level: how the parts of the Dien Hong System talk to each other when a family member views the current condition of one of their linked residents. The lifelines are the elements of the physical design. The family member's choice of resident is shown in the activity diagram AD-UC-08, not here.

```mermaid
sequenceDiagram
  actor FAM as Residents' Family
  participant W as Web App
  participant API as API Server
  participant DB as Database
 
  FAM->>W: Select one of own linked residents on Resident condition (S-F04-02)
  W->>API: getResidentCondition(residentId)
  API->>API: Check session token and role permission
  API->>DB: Read link between the family account and the selected resident (FR-04-01)
  DB-->>API: Link found or not found
 
  alt Access not verified
    API-->>W: Rejected: access denied (FR-04-01)
    W-->>FAM: Show the access-denied message
  else Access verified
    API->>DB: Read the most recent Resident Condition Record of the resident (FR-04-02)
    DB-->>API: Latest record or none
 
    alt Condition recorded
      API-->>W: Current condition (FR-04-02)
      W-->>FAM: Show the current condition of the selected resident
    else None recorded
      API-->>W: No condition recorded yet (FR-04-02)
      W-->>FAM: Tell the family member that no condition has been recorded yet
    end
  end
```

Both alt fragments are outcomes decided inside the system, so they belong in a sequence diagram. They are the two decisions of AD-UC-08: the outer fragment is d1 "Access verified?" (FR-04-01) and the inner one is d2 "Condition recorded?" (FR-04-02). Choosing one of several linked residents is the family member's choice (a1 in AD-UC-08), so it is the first message and not a fragment. The use case only reads: the API Server takes the latest record and changes nothing (earlier records are kept by FR-02-03).
