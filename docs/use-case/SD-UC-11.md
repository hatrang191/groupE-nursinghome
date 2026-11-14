# Sequence Diagram: SD-UC-11 Set up accounts

The Admin's choices (which operation, which role, which residents, confirm or not confirm a deactivation, correct and resubmit or cancel) are shown in the activity diagram [AD-UC-11](../activity/AD-UC-11.md), not here.

| Field    | Value                                                                |
| -------- | -------------------------------------------------------------------- |
| Use case | UC-11 Set up accounts                                                |
| Actor    | Admin                                                                |
| Version  | v1                                                                   |
| FRs      | FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06, FR-06-07, FR-06-08 |

```mermaid
sequenceDiagram
  actor A as Admin
  participant W as Web App
  participant S as API Server
  participant D as Database
  A->>W: request account management
  W->>S: list accounts
  Note over S: every request: check session token and role-based permission
  break role has no permission to manage accounts (FR-06-03)
    S-->>W: rejected: access denied
    W-->>A: home screen with access-denied message
  end
  S->>D: read accounts with role and status
  D-->>S: accounts
  S-->>W: accounts
  W-->>A: account list
  alt create a new account (FR-06-01)
    A->>W: enter account details and role, select residents if role is Residents' Family, submit
    W->>S: create account with details
    S->>D: read accounts with the same identifier
    D-->>S: existing account or none
    alt incomplete or invalid details, identifier already used, or family account with no resident (FR-06-04)
      S-->>W: rejected: each error reported, nothing saved, no log entry
      W-->>A: show errors
    else details valid
      Note over S,D: one transaction: account, links and log entry are saved together or not at all
      S->>D: save account [Active] with role, sign-in enabled
      opt role is Residents' Family (FR-06-05)
        S->>D: save link between account and each selected resident
      end
      S->>D: write action log entry: admin, account, created, time (FR-06-08)
      S-->>W: account created
    end
  else edit an Active account (FR-06-06)
    A->>W: select account, change information, submit
    W->>S: update account(account ID, changes)
    S->>D: read other accounts with the same identifier
    D-->>S: existing account or none
    alt incomplete or invalid changes, or identifier already used by another account (FR-06-04)
      S-->>W: rejected: each error reported, nothing saved, no log entry
      W-->>A: show errors
    else changes valid
      Note over S,D: one transaction: changes and log entry are saved together or not at all
      S->>D: save changes, role and linked residents unchanged
      S->>D: write action log entry: admin, account, edited, time (FR-06-08)
      S-->>W: account updated
    end
  else deactivate an Active account (FR-06-07)
    A->>W: select account, confirm deactivation
    W->>S: deactivate account(account ID)
    Note over S,D: one transaction: status, sessions and log entry are saved together or not at all
    S->>D: set account to Inactive, keep account data and history
    S->>D: end every open session of the account
    S->>D: write action log entry: admin, account, deactivated, time (FR-06-08)
    S-->>W: account deactivated
  end
  W-->>A: result
```

The `alt` fragments inside each operation are outcomes decided inside the system (valid or not,
identifier already used or not), so they belong in a sequence diagram. 
The three top-level branches are the operation the Admin asks for, which is an Admin choice ; they are shown together only so that one diagram covers all FRs of UC-11. 
The `break` is the system's refusal when the role has no permission (D1 [No]). If the Admin does not confirm a deactivation, no request is sent, so that path does not appear here. After a rejection the Admin can correct and submit again or cancel without saving.

Notes:

- A rejected or refused request changes nothing and writes no action log entry.
- Edit and deactivate only offer Active accounts (A4, A5 in the activity diagram), so the diagram
  does not check the status again.
- After deactivation, FR-06-07 makes the system reject any later sign-in or request made with the account, so those checks are not repeated here.
- Changing the role of an existing account is UC-12 (FR-06-02), not UC-11.
