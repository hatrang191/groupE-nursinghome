# Activity Diagram: AD-UC-12 Assign a role to an account

| Field | Value |
| :---- | :---- |
| AD ID | AD-UC-12 |
| Use case | UC-12 Assign a role to an account |
| Feature | F-06 System Administration |
| Primary actor | System Admin |
| Lanes | System Admin · Dien Hong System |
| Inputs (L3-L5) | Context Diagram (Admin) · Use Case Diagram · Sequence: SD-UC-12 · Architecture · W1 rows FR-06-02, FR-06-03, FR-06-08 |
| Trigger | The Admin needs to assign a role to an account, including when a staff member changes position |
| Preconditions | The Admin is signed in. The selected account is Active. |
| Success end state | The selected account holds the assigned role and only the permissions that come with it; the permissions of the previous role no longer apply. The change takes effect on the account's next request, with no sign-out needed. The action is recorded in the action log. |
| Failure end state | No role is changed; the Admin is told why (not enough permission, or the request is outside the permitted scope). |
| FRs covered | FR-06-02, FR-06-03, FR-06-08 |
| Business rules applied |  |
| Version / status | v0.2 · Draft |
| Author | Le Thi Thuy Linh |

## 1. Diagram

```mermaid
swimlane-beta TB
  subgraph ADM["System Admin"]
    start@{ shape: sm-circ }
    A1("A1: Identify the active account to assign a role to")
    A3("A3: Select role and confirm request")
  end
  subgraph SYS["Dien Hong System"]
    D1{"D1: Does Admin have permission<br>to assign roles? [FR-06-03]"}
    S1("S1: Reject request due to insufficient permission [FR-06-03]")
    M1{" "}
    S6("S6: Identify current role and the roles Admin is allowed to assign [FR-06-02, FR-06-03]")
    D2{"D2: Is the role assignment request<br>within Admin's permissions? [FR-06-03]"}
    S5("S5: Reject role assignment request outside the permitted scope [FR-06-03]")
    S2("S2: Replace the account's current role and permissions with the selected role and its permissions [FR-06-02]")
    O1["Role [Authorized]"]
    S7("S7: Record the action in the action log [FR-06-08]")
    O2["Role [Unauthorized]"]
    M2{" "}
    stop1@{ shape: fr-circ }
  end
  start --> A1 --> D1
  D1 -->|"[No]"| S1 --> O2 --> M2
  D1 -->|"[Yes]"| M1 --> S6 --> A3 --> D2
  D2 -->|"[No]"| S5 --> M1
  D2 -->|"[Yes]"| S2 --> O1 --> S7 --> M2
  M2 --> stop1
```

Reading notes:

- D1 and D2 are **decision** nodes. Every outgoing flow carries a guard.
* `M1` is a **merge** node. The flow coming back from S5 (request rejected as outside the permitted scope) and the flow from D1 \[Yes\] both enter `M1` before S6, so S6 is never waiting for two incoming control flows (OMG UML 2.5.1, sub clause 15.2.3.2). The flow `S5 --> M1` lets the Admin choose a role again.
- `O1` and `O2` are **object nodes**. After S2 succeeds, the object `Role` is in state `Authorized` (O1). After S1 rejects the request, the object `Role` is in state `Unauthorized` (O2).
- S2 **replaces** the old role and permissions instead of only saving the new ones, so a staff member who changes position never keeps the permissions of the old position.
- This diagram has no parallel behaviour, so it has no fork and no join.


## 2. Node trace

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| A1 | Action | Identify the active account to assign a role to | FR-06-02 |
| D1 | Decision | Does Admin have permission to assign roles? | FR-06-03 |
| S1 | Action | Reject request due to insufficient permission | FR-06-03 |
| O2 | Object node | Role [Unauthorized] | FR-06-03 (state value) |
| M1 | Merge | (no text) | n/a |
| S6 | Action | Identify current role and the roles Admin is allowed to assign | FR-06-02, FR-06-03 |
| A3 | Action | Select role and confirm request | FR-06-02 |
| D2 | Decision | Is the role assignment request within Admin's permissions? | FR-06-03 |
| S5 | Action | Reject role assignment request outside the permitted scope | FR-06-03 |
| S2 | Action | Replace the account's current role and permissions with the selected role and its permissions | FR-06-02 |
| O1 | Object node | Role [Authorized] | FR-06-02 (state value) |
| S7 | Action | Record the action in the action log | FR-06-08 |
| M2 | Merge | (no text) | n/a |

Proposed FRs that are not yet in W1:

| L2 ID | L2 function name | EARS acceptance criterion |
| :---- | :---- | :---- |
| FR-06-08 | Record account and role changes in the action log | WHEN an account is created, edited or deactivated, or its role is changed, the system SHALL record who did it, which account, what was done and when. |

## 3. Branches and acceptance tests

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| D1 \[No\] | the System admin does not have permission to assign roles | the System admin selects an account to assign a role | no role is changed, the request is rejected with an insufficient-permission message, and the Role is in state Unauthorized |
| D1 \[Yes\] → D2 \[No\] → S5 → M1 → S6 | the System admin may assign roles but not the role they choose | the System admin selects that role and confirms | no role is changed; the request is rejected as outside the permitted scope, the flow goes from S5 through M1 back to S6, and the Admin can choose again |
| D1 \[Yes\] → D2 \[Yes\] | the System admin may assign roles, the chosen role is within their permissions, and the active account currently holds the role "Nurse" | the System admin selects the role "Receptionist" and confirms | the account holds the role "Receptionist" and its permissions only, the permissions of "Nurse" no longer apply on the account's next request, the Role is in state Authorized, and the action is recorded in the action log |

## 4. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Role | Authorized (after S2), Unauthorized (after S1) | Authorized, Unauthorized |

## 5. Screens touched

| Admin action | Screen ID |
| :---- | :---- |
| Identify the account to assign a role to |  |
| Select role and confirm request |  |

## 6. Open questions

| #   | Question                                                                                                                                          | Blocking? | Default applied                                                                 |
| :-- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :-------- | :------------------------------------------------------------------------------ |
| 1   | [NEEDS CLARIFICATION: after a request is rejected as outside the permitted scope (S5), can the Admin pick a different role for the same account?] | No        | Yes: the flow goes from S5 through M1 back to S6 so the Admin can choose again. |
| 2   | [NEEDS CLARIFICATION: are rejected requests (S1, S5) also recorded in the action log?]                                                            | No        | No. Only completed assignments are recorded (FR-06-08).                         |
| 3   | [NEEDS CLARIFICATION: can a role be assigned to an Inactive account?]                                                                             | No        | No. Only Active accounts are offered in A1.                                     |

## 7. Verification log

| Check                                             | Result         | By  | Date |
| :------------------------------------------------ | :------------- | :-- | :--- |
| Renders on mermaid.live                           | Pass           |     |      |
| Every decision has guards on all outgoing flows   | Pass           |     |      |
| Every fork has a matching join                    | Pass (no fork) |     |      |
| Every system action cites an FR                   | Pass           |     |      |
| Every FR of UC-12 appears in the diagram          | Pass (3 of 3)  |     |      |
| Lane names match the Context Diagram              | Pass           |     |      |
| AI critic prompt run, findings resolved or logged | Pass           |     |      |

## Notes for the integrator: changes needed in W1

| FR       | Function                                          | Change                                                             |
| :------- | :------------------------------------------------ | :----------------------------------------------------------------- |
| FR-06-08 | Record account and role changes in the action log | **New function (F-06, Must).** Covers S7 here and S6 in AD-UC-11.  |
