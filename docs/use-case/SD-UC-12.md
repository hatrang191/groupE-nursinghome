# Sequence Diagram: SD-UC-12 Assign role-based permissions

The Admin's choices (which account, which role, confirm or choose again after a rejection) are shown in the activity diagram [AD-UC-12](../activity/AD-UC-12.md), not here.

| Field | Value |
|---|---|
| Use case | UC-12 Assign role-based permissions |
| Actor | Admin |
| Version | v1 (aligned with AD-UC-12 v0.2) |
| FRs | FR-06-02, FR-06-03, FR-06-08 |

```mermaid
sequenceDiagram
  actor A as Admin
  participant W as Web App
  participant S as API Server
  participant D as Database
  A->>W: request role assignment
  W->>S: list Active accounts
  Note over S: every request: check session token and role-based permission
  break Admin has no permission to assign roles (FR-06-03)
    S-->>W: rejected: insufficient permission
    W-->>A: home screen of the role with access-denied message
  end
  S->>D: read Active accounts with current role
  D-->>S: accounts
  S-->>W: accounts
  W-->>A: account list
  A->>W: select account
  W->>S: get current role and assignable roles(account ID)
  S->>D: read current role of the account and the roles this Admin may assign
  D-->>S: current role, assignable roles
  S-->>W: current role and assignable roles (FR-06-02, FR-06-03)
  W-->>A: show current role and the roles to choose from
  A->>W: select role, confirm
  W->>S: assign role(account ID, role)
  S->>D: read the roles this Admin may assign
  D-->>S: assignable roles
  alt selected role is outside the roles this Admin may assign (FR-06-03)
    S-->>W: rejected: outside the permitted scope, no role changed, no log entry
  else selected role is within the permitted scope
    Note over S,D: one transaction: role change and log entry are saved together or not at all
    S->>D: replace the account's role with the selected role (FR-06-02)
    Note over D: permissions of the previous role no longer apply, session stays open
    S->>D: write action log entry: admin, account, role changed from old role to new role, time (FR-06-08)
    S-->>W: role assigned
  end
  W-->>A: result
```

The `break` is the system's refusal when the Admin's role has no permission to assign roles (D1 [No]).
The `alt` is an outcome decided inside the system: the API Server checks again, on its own, that the selected role is one this Admin may assign (D2), so the check does not depend on what the screen offered. The Admin's own choices are not drawn: after a rejection as outside the permitted scope the Admin can choose another role, which is a new run of the second half of this diagram from "select role, confirm".

Notes:

- The role is read from the Database on every request, so the change applies at
  the account's next request and the account does not have to sign out. 
- A rejected request changes nothing and writes no action log entry.
- Only Active accounts are listed, so the diagram does not check the account status again.
- Creating an account with a role and editing account information are UC-11, not UC-12.
