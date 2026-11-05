# Activity Diagram: AD-UC-10 Set up accounts

| Field | Value |
| :---- | :---- |
| AD ID | AD-UC-11 |
| Use case | UC-11 Set up accounts |
| Feature | F-06 System Administration |
| Primary actor | System admin |
| Lanes | System admin · Dien Hong system |
| Inputs (L3-L5) | Context Diagram (System admin ↔ Dien Hong system: user accounts, role permissions) · Use Case Diagram ("Set up accounts") · Sequence: SD-UC-011 · W1 rows FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06 |
| Trigger | The System admin wants to create a new account |
| Preconditions | The System admin is signed in. For a family account, the resident already exists (UC-01). |
| Success end state | One new account exists with the name, role and credentials that were entered; a family account is also linked to the selected resident; the System admin has seen a confirmation. |
| Failure end state | No new account is created; the System admin is told why (no permission, or the information was invalid and the creation was cancelled). |
| FRs covered | FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06 |
| Business rules applied |  |
| Version / status | v0.1 |
| Author | Le Thi Thuy LInh |

1\. Diagram

```mermaid
swimlane-beta TB
  subgraph ADM["System admin"]
    start@{ shape: sm-circ }
    A1("A1: Request account creation")
    A2("A2: Provide account information and, for a family account, the resident or residents to link")
    A3("A3: Adjust account information")
    D3{"D3: Continue creating the account? [FR-06-05]"}
  end
  subgraph SYS["Dien Hong system"]
    D1{"D1: Does Admin have permission<br>to create accounts? [FR-06-03]"}
    S1("S1: Reject request [FR-06-03]")
    M1{" "}
    D2{"D2: Is the information complete and valid,<br>and is the identifier not yet in use? [FR-06-04]"}
    S2("S2: Identify and report information errors [FR-06-04]")
    D4{"D4: Is the new account for a family member? [FR-06-06]"}
    S5("S5: Attach the selected resident or residents [FR-06-06]")
    M3{" "}
    S3("S3: Create and save new account [FR-06-01]")
    S4("S4: Confirm account has been created [FR-06-01]")
    O1["Account [Active]"]
    O2["Account [Rejected]"]
    O3["Account [Invalid]"]
    M2{" "}
    stop1@{ shape: fr-circ }
  end
  start --> A1 --> D1
  D1 -->|"[No]"| S1 --> O2 --> M2
  D1 -->|"[Yes]"| A2 --> M1 --> D2
  D2 -->|"[No]"| S2 --> O3 --> D3
  D2 -->|"[Yes]"| D4
  D4 -->|"[Yes]"| S5 --> M3
  D4 -->|"[No]"| M3
  M3 --> S3 --> O1 --> S4 --> M2
  D3 -->|"[No]"| M2
  M2 --> stop1
  D3 -->|"[Yes]"| A3 --> M1
```

&nbsp;

Reading notes:

- `M1` is a **merge** node. The first submission (from A2) and the corrected submission (from A3) both enter `M1` before the decision D2, so D2 never has two incoming flows directly (OMG UML 2.5.1, sub clause 15.2.3.2). The loop edge `A3 --> M1` is written last in its group to keep the layout readable.  
- `M3` is a **merge** node. The family branch (through S5) and the other branch (D4 \[No\]) both enter `M3` before S3, so S3 has one incoming flow.  
- `M2` is a **merge** node. The three ending paths (S1 → O2 rejection, D3 \[No\] cancellation and S4 success) are joined before the final node.  
- `D3` is the System admin's own choice after errors are reported. A user's choice is a decision  in the activity diagram, not an `alt` in the sequence diagram.  
- `O1`, `O2` and `O3` are **object nodes** for the object `Account`. After S3 saves the account it is `Active` (O1). After S1 rejects the request it is `Rejected` (O2). After S2 reports errors it is `Invalid` (O3); if the System admin then cancels at D3 \[No\], it stays `Invalid` and is never saved.  
- This diagram has no parallel behaviour, so it has no fork and no join.

## 2\. Node trace

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| A1 | Action | Request account creation | FR-06-01 |
| D1 | Decision | Does Admin have permission to create accounts? | FR-06-03 |
| S1 | Action | Reject request | FR-06-03 |
| O2 | Object node | Account \[Rejected\] | FR-06-03 (state value) |
| A2 | Action | Provide account information and, for a family account, the resident or residents to link | FR-06-01, FR-06-06 |
| M1 | Merge | (no text) | n/a |
| D2 | Decision | Is the information complete and valid, and is the identifier not yet in use? | FR-06-04 |
| S2 | Action | Identify and report information errors | FR-06-04 |
| O3 | Object node | Account \[Invalid\] | FR-06-04 (state value) |
| D3 | Decision | Continue creating the account? | FR-06-05 |
| A3 | Action | Adjust account information | FR-06-05 |
| D4 | Decision | Is the new account for a family member? | FR-06-06 |
| S5 | Action | Attach the selected resident or residents to the new account | FR-06-06 |
| M3 | Merge | (no text) | n/a |
| S3 | Action | Create and save new account | FR-06-01 |
| O1 | Object node | Account \[Active\] | FR-06-01 (state value) |
| S4 | Action | Confirm account has been created | FR-06-01 |
| M2 | Merge | (no text) | n/a |

Proposed FRs that are not yet in W1:

| L2 ID | L2 function name | EARS acceptance criterion |
| :---- | :---- | :---- |
| FR-06-04 | Validate new account information | IF the new account information is incomplete or invalid, or its identifier is already in use, THEN the system SHALL NOT save the account and SHALL report each error. |
| FR-06-05 | Correct or cancel account creation | WHEN the system has reported errors in the new account information, the system SHALL let the admin correct the information and submit it again, or cancel the creation without saving an account. |
| FR-06-06 | Link family account to resident | WHEN an admin creates an account for a family member and selects one or more residents, the system SHALL save the link between the account and each selected resident. |

3\. Branches and acceptance tests

One row per outgoing branch of every decision. DBIZ3 turns each row into at least one test.

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| D1 \[No\] | a System admin account whose role has no permission to create accounts | it requests account creation | no account is created, the request is rejected, and the Account is in state Rejected |
| D1 \[Yes\] → D2 \[No\] → D3 \[Yes\] | the System admin submits a new account with identifier "nurse01", which another account already uses | the System admin chooses to continue and changes the identifier to "nurse02" | the Account is in state Invalid after the error "identifier already in use" is reported for "nurse01"; the corrected submission goes through validation again |
| D1 \[Yes\] → D2 \[No\] → D3 \[No\] | the System admin submits a new account with the role field empty and sees the error | the System admin chooses not to continue | no account is created, the Account stays in state Invalid, and the flow ends |
| D1 \[Yes\] → D2 \[No\] (family account without resident) | the System admin submits a family account "lan.family" for "Nguyễn Thị Lan" and selects no resident | the System admin submits the information | no account is saved, the error "resident required" is reported, and the Account is in state Invalid |
| D1 \[Yes\] → D2 \[Yes\] → D4 \[No\] | the System admin has permission and submits name "Tran Thi B", role "Nurse", identifier "nurse03" (not in use) | the System admin submits the information | one account "nurse03" with role "Nurse" is saved with no resident link, the Account is in state Active, and a confirmation is shown |
| D1 \[Yes\] → D2 \[Yes\] → D4 \[Yes\] | the resident "Nguyễn Văn An" exists and the System admin submits name "Nguyễn Thị Lan", role "Family", identifier "lan.family", resident "Nguyễn Văn An" | the System admin submits the information | one account "lan.family" is saved and linked to "Nguyễn Văn An" only, the Account is in state Active, and the access check in AD-UC-08 passes for that resident when she opens the mobile app |

## 4\. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Account | Active (after S3), Invalid (after S2), Rejected (after S1) | Active, Invalid, Rejected |

## 5\. Screens touched

Filled in during Lesson 8, from the Screen List.

| System admin action | Screen ID |
| :---- | :---- |
| Request account creation |  |
| Provide account information (and the resident to link), Adjust account information |  |

## 6\. Open questions

| \# | Question | Blocking? | Default applied |
| :---- | :---- | :---- | :---- |
| 1 | \[NEEDS CLARIFICATION: which identifier must be unique: the login name only, or also the email address?\] | No | The email address only. |
| 2 | \[NEEDS CLARIFICATION: how many times may the System admin correct and resubmit in one creation?\] | No | No limit. |
| 3 | \[NEEDS CLARIFICATION: should the data model store `Invalid` and `Rejected`, for example in a creation-attempt log, or only `Active`?\] | No | Only `Active` is stored on the account; `Invalid` and `Rejected` are not saved. |
| 4 | \[NEEDS CLARIFICATION: can one family account be linked to more than one resident?\] | No | No limit. |

7\. Verification log

| Check | Result | By | Date |
| :---- | :---- | :---- | :---- |
| Renders on mermaid.live | Pass |  |  |
| Every decision has guards on all outgoing flows | Pass |  |  |
| Every fork has a matching join | Pass (no fork) |  |  |
| Every system action cites an FR | Pass |  |  |
| Every FR of UC-10 appears in the diagram | Pass (5 of 5\) |  |  |
| Lane names match the Context Diagram | Pass |  |  |
| AI critic prompt run, findings resolved or logged as open questions | Pending |  |  |

Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes the UC-10 diagram needs in the function table.

| FR | Function | Change |
| :---- | :---- | :---- |
| FR-06-04 | Validate new account information | **New function (F-06, Must).** Covers D2 and S2. |
| FR-06-05 | Correct or cancel account creation | **New function (F-06, Should).** Covers D3 and A3. |
| FR-06-06 | Link family account to resident | **New function (F-06, Must).** Covers D4 and S5, and gives AD-UC-08 the link it needs for its access check. |

&nbsp;