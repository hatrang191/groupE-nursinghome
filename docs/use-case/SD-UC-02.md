# Sequence Diagram: SD-UC-02 Update resident information

| Field | Value |
|---|---|
| Use case | UC-02 Update resident information |
| Version | v1 |
| FRs | FR-01-02 |

The lifelines follow the Dien Hong use-case diagram's sequence-diagram guidance: Web App, API Server and Database. The API Server's session-token and role-permission check is shown based on the project-wide assumption for use cases that require login; it is not labelled as FR-06-03 because that FR is scoped to UC-11, UC-12 and UC-13.

```mermaid
sequenceDiagram
  actor REC as Receptionist
  participant W as Web App
  participant API as API Server
  participant DB as Database

  REC->>W: Select a resident
  W->>API: getResidentProfile(residentId)
  API->>API: Check session token and role permission
  API->>DB: Read Resident Profile
  DB-->>API: Current resident information
  API-->>W: Current resident information
  W-->>REC: Show current resident information

  REC->>W: Edit information and save changes
  W->>API: updateResidentProfile(residentId, changes)
  API->>API: Check session token and role permission
  API->>API: Validate changes (FR-01-02)

  alt Changes are invalid
    API-->>W: Validation errors (FR-01-02)
    W-->>REC: Display validation errors
    REC->>W: Correct information and save again
    W->>API: updateResidentProfile(residentId, correctedChanges)
    API->>API: Check session token and role permission
    API->>API: Validate changes (FR-01-02)
  end

  alt Changes are valid
    API->>DB: Update Resident Profile with changes, time and staff name (FR-01-02)
    DB-->>API: Updated profile
    API-->>W: Updated resident information
    W-->>REC: Show updated information
  else Changes remain invalid
    API-->>W: Validation errors (FR-01-02)
    W-->>REC: Display validation errors
  end
```

## Reading notes

- The diagram follows AD-UC-02: select resident, show current information, edit, save, validate, then either display errors or store the changes and show the updated information.
- The `alt` fragments represent validation outcomes decided by the system.
- The supplied sources do not enumerate which fields may be updated or the detailed validation rules.
- The exact timestamp format and audit-retention rule are not specified; the diagram retains the stated requirement to store the time and staff name.
