# Activity Diagram: AD-UC-11 Set up accounts

| Field                  | Value                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| :--------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AD ID                  | AD-UC-11                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Use case               | UC-11 Set up accounts                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Feature                | F-06 System Administration                                                                                                                                                                                                                                                                                                                                                                                                              |
| Primary actor          | Admin                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Lanes                  | Admin · Dien Hong system                                                                                                                                                                                                                                                                                                                                                                                                         |
| Inputs (L3-L5)         | Context Diagram (Admin ↔ Dien Hong system: user accounts, role permissions) · Use Case Diagram ("Set up accounts") · Sequence: SD-UC-11 · W1 rows FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06, FR-06-07, FR-06-08                                                                                                                                                                                                          |
| Trigger                | The Admin submits a request to create a new account, change the information of an existing account, or deactivate an account                                                                                                                                                                                                                                                                                                                 |
| Preconditions          | The Admin is signed in. For a family account, the resident already exists (UC-01). For edit and deactivate, the account exists and is Active.                                                                                                                                                                                                                                                                                    |
| Success end state      | Create: one new account exists with the name, role and credentials that were entered; a family account is also linked to the selected resident. Edit: the account holds the changed information, with its role and links unchanged. Deactivate: the account is Inactive, its open sessions are ended and it can no longer sign in. In every case the action is recorded in the action log and the System admin has seen a confirmation. |
| Failure end state      | Nothing is created, changed or deactivated; the Admin is told why (no permission, or the information was invalid and the operation was cancelled).                                                                                                                                                                                                                                                                               |
| FRs covered            | FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06, FR-06-07, FR-06-08                                                                                                                                                                                                                                                                                                                                                                    |
| Business rules applied |                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Version / status       | v0.2 · Draft                                                                                                                                                                                                                                                                                                                                                                                                                            |
| Author                 | Le Thi Thuy Linh                                                                                                                                                                                                                                                                                                                                                                                                                        |

## 1. Diagram

```mermaid
swimlane-beta TB
  subgraph ADM["Admin"]
    start@{ shape: sm-circ }
    A1("A1: Request account management and choose an operation")
    A2("A2: Provide account information and, for a family account, the resident or residents to link")
    A4("A4: Select an active account and provide the changes")
    A5("A5: Select an active account to deactivate")
    A3("A3: Adjust account information")
    D3{"D3: Continue creating or editing the account? [FR-06-04]"}
    D6{"D6: Does Admin confirm the deactivation? [FR-06-07]"}
  end
  subgraph SYS["Dien Hong system"]
    D1{"D1: Does Admin have permission<br>to manage accounts? [FR-06-03]"}
    S1("S1: Reject request [FR-06-03]")
    D4{"D4: Which operation did Admin choose?"}
    M1{" "}
    D2{"D2: Is the information complete and valid,<br>and is the identifier not used by another account? [FR-06-04, FR-06-05]"}
    S2("S2: Identify and report information errors [FR-06-04]")
    D5{"D5: Is this a new account?"}
    S3("S3: Create and save new account, with its resident links for a family account [FR-06-01, FR-06-05]")
    S5("S5: Save the changed information [FR-06-06]")
    S7("S7: Deactivate account")
    M2{" "}
    S6("S6: Record the action in the action log [FR-06-08]")
    S4("S4: Confirm that the operation is complete [FR-06-01, FR-06-06, FR-06-07]")
    O1["Account [Active]"]
    O2["Account [Inactive]"]
    stop1@{ shape: fr-circ }
  end
  start --> A1 --> D1
  D1 -->|"[No]"| S1 --> stop1
  D1 -->|"[Yes]"| D4
  D4 -->|"[Create]"| A2 --> M1
  D4 -->|"[Edit]"| A4 --> M1
  D4 -->|"[Deactivate]"| A5 --> D6
  D6 -->|"[No]"| stop1
  D6 -->|"[Yes]"| S7 --> O2 --> M2
  M1 --> D2
  D2 -->|"[No]"| S2 --> D3
  D2 -->|"[Yes]"| D5
  D5 -->|"[Yes]"| S3 --> O1 --> M2
  D5 -->|"[No]"| S5 --> M2
  M2 --> S6 --> S4 --> stop1
  D3 -->|"[No]"| stop1
  D3 -->|"[Yes]"| A3 --> M1
```

&nbsp;

Reading notes:

- Every outgoing flow carries a guard.
- `M1` is a **merge** node. The first submission for a new account (A2), the changes for an existing account (A4) and the corrected submission (A3) all enter `M1` before the decision D2, so D2 never has two incoming flows directly (OMG UML 2.5.1, sub clause 15.2.3.2). Create and edit share one validation (D2) and one correction loop (D3, A3). 
- `D5` separates a new account (create path, S3) from an existing account (edit path, S5). 
- `M2` is a **merge** node. The three success paths (create through O1, edit through S5, deactivate through O2) join before S6 and S4.
- `O1` and `O2` are **object nodes** for the object `Account`. Only states that are stored are shown: after S3 saves a new account it is `Active` (O1), after S7 it is `Inactive` (O2). A rejected request (S1) or an invalid submission that is cancelled (S2, D3 \[No\]) creates nothing. 
- Edit and deactivate only offer `Active` accounts (A4, A5). Edit never changes the role: a role change is UC-12.
- A deactivated account is never deleted, so its data and log entries stay linked to the person for UC-14.
- This diagram has no parallel behaviour, so it has no fork and no join.

## 2. Node trace

| Node | Kind        | Text                                                                                                                          | Traces to                              |
| :--- | :---------- | :---------------------------------------------------------------------------------------------------------------------------- | :------------------------------------- |
| A1   | Action      | Request account management and choose an operation (create, edit or deactivate)                                               | FR-06-01, FR-06-06, FR-06-07           |
| D1   | Decision    | Does Admin have permission to manage accounts?                                                                                | FR-06-03                               |
| S1   | Action      | Reject request                                                                                                                | FR-06-03                               |
| D4   | Decision    | Which operation did Admin choose?                                                                                             | FR-06-01, FR-06-06, FR-06-07 (routing) |
| A2   | Action      | Provide account information and, for a family account, the resident or residents to link                                      | FR-06-01, FR-06-05                     |
| A4   | Action      | Select an active account and provide the changes (name, contact information or a new password)                                | FR-06-06                               |
| A5   | Action      | Select an active account to deactivate                                                                                        | FR-06-07                               |
| D6	 | Decision	   | Does Admin confirm the deactivation?	                                                                                         | FR-06-07 
| M1   | Merge       | (no text)                                                                                                                     | n/a                                    |
| D2   | Decision    | Is the information complete and valid (a family account needs a resident), and is the identifier not used by another account? | FR-06-04, FR-06-05                     |
| S2   | Action      | Identify and report information errors                                                                                        | FR-06-04                               |
| D3   | Decision    | Continue creating or editing the account?                                                                                     | FR-06-04                               |
| A3   | Action      | Adjust account information                                                                                                    | FR-06-04                               |
| D5   | Decision    | Is this a new account?                                                                                                        | FR-06-01, FR-06-06 (routing)           |
| S3   | Action      | Create and save new account, with its resident links for a family account                                                     | FR-06-01, FR-06-05                     |
| O1   | Object node | Account [Active]                                                                                                              | FR-06-01 (state value)                 |
| S5   | Action      | Save the changed information                                                                                                  | FR-06-06                               |
| S7   | Action      | Deactivate account and end its sessions                                                                                       | FR-06-07                               |
| O2   | Object node | Account [Inactive]                                                                                                            | FR-06-07 (state value)                 |
| M2   | Merge       | (no text)                                                                                                                     | n/a                                    |
| S6   | Action      | Record the action in the action log                                                                                           | FR-06-08                               |
| S4   | Action      | Confirm that the operation is complete                                                                                        | FR-06-01, FR-06-06, FR-06-07           |

Proposed FRs that are not yet in W1:

| L2 ID | L2 function name | EARS acceptance criterion |
| :---- | :---- | :---- |
| FR-06-04 | Validate and correct account information | IF the information of a new or edited account is incomplete or invalid, including a family account with no selected resident, or its identifier is already used by another account, THEN the system SHALL NOT save it and SHALL report each error. WHEN the errors have been reported, the system SHALL let the admin correct the information and submit it again, or cancel the operation without saving. |
| FR-06-05 | Link family account to resident | WHEN an admin creates an account for a family member and selects one or more residents, the system SHALL save the link between the account and each selected resident. |
| FR-06-06 | Edit account information | WHEN an admin submits valid changes to the name, contact information or password of an Active account, the system SHALL save the changes and SHALL keep the role and linked residents of the account unchanged. |
| FR-06-07 | Deactivate account | WHEN an admin deactivates an Active account, the system SHALL set the account to Inactive, SHALL end every open session of the account, SHALL reject any later sign-in or request made with it, and SHALL keep the account data and history. |
| FR-06-08 | Record account and role changes in the action log | WHEN an account is created, edited or deactivated, or its role is changed, the system SHALL record who did it, which account, what was done and when. |

## 3. Branches and acceptance tests

One row per outgoing branch of every decision. DBIZ3 turns each row into at least one test.

| Branch                                                             | Given                                                                                                                                                    | When                                                                         | Then                                                                                                                                                                                                                  |
| :----------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| D1 \[No\]                                                          | an Admin account whose role has no permission to manage accounts                                                                                   | it requests an account operation                                             | nothing is created, changed or deactivated, and the request is rejected with a message                                                                                                                                |
| D1 \[Yes\] → D4 \[Create\] → D2 \[No\] → D3 \[Yes\] → A3 → M1 → D2 | the Admin submits a new account with identifier "nurse01", which another account already uses                                                     | the Admin chooses to continue and changes the identifier to "nurse02" | the error "identifier already in use" is reported for "nurse01" and nothing is saved; the corrected submission goes from A3 through M1 back to D2 and is validated again                                              |
| D1 \[Yes\] → D4 \[Create\] → D2 \[No\] → D3 \[No\]                 | the Admin submits a new account with the role field empty and sees the error                                                                      | the Admin chooses not to continue                                     | no account is created and the flow ends                                                                                                                                                                               |
| D4 \[Create\] → D2 \[No\] (family account without resident)        | the Admin submits a family account "lan.family" for "Nguyễn Thị Lan" and selects no resident                                                      | the Admin submits the information                                     | no account is saved and the error "resident required" is reported                                                                                                                                                     |
| D2 \[Yes\] → D5 \[Yes\]                                            | the Admin has permission and submits name "Tran Thi B", role "Nurse", identifier "nurse03" (not in use)                                           | the Admin submits the information                                     | one account "nurse03" with role "Nurse" is saved with no resident link, the Account is in state Active, the action is recorded in the action log, and a confirmation is shown                                         |
| D2 \[Yes\] → D5 \[Yes\]                                            | the resident "Nguyễn Văn An" exists and the Admin submits name "Nguyễn Thị Lan", role "Family", identifier "lan.family", resident "Nguyễn Văn An" | the Admin submits the information                                     | one account "lan.family" is saved and linked to "Nguyễn Văn An" only, the Account is in state Active, the action is recorded, and the access check in AD-UC-08 passes for that resident when she opens the mobile app |
| D1 \[Yes\] → D4 \[Edit\] → D2 \[Yes\] → D5 \[No\]                  | the active account "nurse03" belongs to a Nurse whose phone number changed                                                                               | the Admin enters the new phone number and a new password and submits  | the changes are saved, the role and any resident links are unchanged, the account stays Active, the action is recorded, and a confirmation is shown                                                                   |
| D4 \[Edit\] → D2 \[No\] → D3 \[Yes\] → A3 → M1 → D2                | the Admin changes the identifier of "nurse03" to "nurse01", which another account already uses                                                    | the Admin chooses to continue and changes the identifier to "nurse04" | the error "identifier already in use" is reported first and nothing is saved; the corrected submission goes from A3 through M1 back to D2, is validated again and is then saved                                       |
| D1 [Yes] → D4 [Deactivate] → D6 [Yes]                                     | the active account "nurse03" belongs to a Nurse who left, and has one open session                                                                       | the Admin deactivates the account                                     | the Account is in state Inactive, the open session is ended, a later sign-in or request with "nurse03" is rejected, the account data and history are kept, the action is recorded, and a confirmation is shown        |
| D4 [Deactivate] → D6 [No]                                     | the active account “nurse03” has one open session                                                                       | the Admin selects it for deactivation and does not confirm                                     | the account stays Active, the session stays open, nothing is recorded in the action log, and the flow ends        |

## 4. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Account | Active (after S3), Inactive (after S7) | Active, Inactive |

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Admin action | Screen ID |
| :---- | :---- |
| Request account management and choose an operation |  |
| Provide account information (and the resident to link), Adjust account information |  |
| Select an account and provide the changes |  |
| Select an account to deactivate |  |

## 6. Open questions

| #   | Question                                                                                                          | Blocking? | Default applied                                                                                      |
| :-- | :---------------------------------------------------------------------------------------------------------------- | :-------- | :--------------------------------------------------------------------------------------------------- |
| 1   | [NEEDS CLARIFICATION: which identifier must be unique: the login name only, or also the email address?]           | No        | The email address only.                                                                              |
| 2   | [NEEDS CLARIFICATION: how many times may the Admin correct and resubmit in one operation?]                 | No        | No limit.                                                                                            |
| 3   | [NEEDS CLARIFICATION: should rejected requests and invalid submissions be stored, for example in an attempt log?] | No        | No. Only `Active` and `Inactive` are stored on the account, and the diagram shows only those states. |
| 4   | [NEEDS CLARIFICATION: can one family account be linked to more than one resident?]                                | No        | No limit.                                                                                            |
| 5   | [NEEDS CLARIFICATION: can the Admin reactivate an Inactive account?]                                              | No        | No. If the person returns, the Admin creates a new account with a new identifier.                    |
| 6   | [NEEDS CLARIFICATION: can an edit change the residents linked to a family account?]                               | No        | No.                                                                                                  |
| 7   | [NEEDS CLARIFICATION: can the Admin edit an Inactive account?]                                                    | No        | No. Only Active accounts are offered in A4 and A5.                                                   |


## 7. Verification log

| Check                                                               | Result         | By  | Date |
| :------------------------------------------------------------------ | :------------- | :-- | :--- |
| Renders on mermaid.live                                             | Pass           |     |      |
| Every decision has guards on all outgoing flows                     | Pass           |     |      |
| Every fork has a matching join                                      | Pass (no fork) |     |      |
| Every system action cites an FR                                     | Pass           |     |      |
| Every FR of UC-11 appears in the diagram                            | Pass (7 of 7)  |     |      |
| Lane names match the Context Diagram                                | Pass           |     |      |
| AI critic prompt run, findings resolved or logged as open questions | Pass           |     |      |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes the UC-11 diagram needs in the function table. 

| FR       | Function                                          | Change                                                                                                                                                                                  |
| :------- | :------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| FR-06-04 | Validate and correct account information          | **New function (F-06, Should).** Covers D2, S2, D3 and A3 for create and edit: the validation (including the rule that a family account needs a resident) and the correct-or-cancel loop. |
| FR-06-05 | Link family account to resident                   | **New function (F-06, Must).** Covers A2, D2 and S3, and gives AD-UC-08 the link it needs for its access check.                                                                         |
| FR-06-06 | Edit account information                          | **New function (F-06, Should).** Covers D4 \[Edit\], A4, D5 \[No\] and S5.                                                                                                              |
| FR-06-07 | Deactivate account                                | **New function (F-06, Must).** Covers D4 \[Deactivate\], A5 and S7.                                                                                                                     |
| FR-06-08 | Record account and role changes in the action log | **New function (F-06, Should).** Covers S6 here and S7 in AD-UC-12.                                     |
|          |                                                   |                                                                                                                                                                                         |

