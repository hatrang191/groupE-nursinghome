# Activity Diagram: AD-UC-13 Monitor radar status

| Field                  | Value                                                                                                                                                                           |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| AD ID                  | AD-UC-13                                                                                                                                                                        |
| Use case               | UC-13 Monitor radar status                                                                                                                                                      |
| Feature                | F-06 System Administration                                                                                                                                                      |
| Primary actor          | Admin                                                                                                                                                                           |
| Lanes                  | Admin · Dien Hong System                                                                                                                                                        |
| Inputs (L3-L5)         | Context Diagram (Admin; Radar) · Use Case Diagram ("Monitor radar status") · Sequence: SD-UC-13 · Architecture (Radar Gateway) · W1 rows FR-06-03, FR-06-09, FR-06-10, FR-06-11 |
| Trigger                | The Admin wants to check whether the radars are working                                                                                                                         |
| Preconditions          | The Admin is signed in. The radars are already registered in the system (set up outside this use case).                                                                         |
| Success end state      | The Admin has seen the current status of every radar and the time of its last data; radars that are Offline are highlighted.                                                    |
| Failure end state      | No radar status is shown; the Admin is told why (no permission, or the status cannot be obtained).                                                                              |
| FRs covered            | FR-06-03, FR-06-09, FR-06-10, FR-06-11                                                                                                                                          |
| Business rules applied |                                                                                                                                                                                 |
| Version / status       | v0.1 · Draft                                                                                                                                                                    |
| Author                 | Le Thi Thuy Linh                                                                                                                                                                |

## 1. Diagram

```mermaid
swimlane-beta TB
  subgraph ADM["Admin"]
    start@{ shape: sm-circ }
    A1("A1: Request radar status")
    A2("A2: Review the status of the radars")
  end
  subgraph SYS["Dien Hong System"]
    D1{"D1: Does Admin have permission<br>to monitor radar status? [FR-06-03]"}
    S1("S1: Reject request due to insufficient permission [FR-06-03]")
    S2("S2: Get the latest status of each radar [FR-06-09]")
    D2{"D2: Is the radar status available? [FR-06-11]"}
    S4("S4: Tell Admin that the radar status cannot be shown [FR-06-11]")
    S3("S3: Show each radar with its status and the time of its last data [FR-06-09]")
    D3{"D3: Is any radar Offline? [FR-06-10]"}
    S5("S5: Highlight the Offline radars [FR-06-10]")
    M1{" "}
    O1["Radar status [Displayed]"]
    O2["Radar status [Rejected]"]
    O3["Radar status [Unavailable]"]
    M2{" "}
    stop1@{ shape: fr-circ }
  end
  start --> A1 --> D1
  D1 -->|"[No]"| S1 --> O2 --> M2
  D1 -->|"[Yes]"| S2 --> D2
  D2 -->|"[No]"| S4 --> O3 --> M2
  D2 -->|"[Yes]"| S3 --> D3
  D3 -->|"[Yes]"| S5 --> M1
  D3 -->|"[No]"| M1
  M1 --> O1 --> A2 --> M2
  M2 --> stop1
```

Reading notes:

*  D1, D2 and D3 are **decision** nodes. Every outgoing flow carries a guard.
- `M1` is a **merge** node. The branch through S5 (some radar is Offline) and the branch D3 \[No\] (all radars are Online) both enter `M1` before the radar status is displayed.
- `M2` is a **merge** node. The three ending paths (S1 → O2 rejection, S4 → O3 status unavailable and A2 success) are joined before the final node.
- `O1`, `O2` and `O3` are **object nodes** for the object `Radar status`. After the status is shown it is `Displayed` (O1). After S1 rejects the request it is `Rejected` (O2). After S4 reports that it cannot be obtained it is `Unavailable` (O3).
- This use case only **reads** the status. The radar data itself arrives on its own, started by the Radar (UC-06), so no radar lane is needed here.
- After a radar is highlighted as Offline, the Admin can continue with UC-14 to look for the cause. 
- This diagram has no parallel behaviour, so it has no fork and no join.

## 2. Node trace

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| A1 | Action | Request radar status | FR-06-09 |
| D1 | Decision | Does Admin have permission to monitor radar status? | FR-06-03 |
| S1 | Action | Reject request due to insufficient permission | FR-06-03 |
| O2 | Object node | Radar status [Rejected] | FR-06-03 (state value) |
| S2 | Action | Get the latest status of each radar | FR-06-09 |
| D2 | Decision | Is the radar status available? | FR-06-11 |
| S4 | Action | Tell Admin that the radar status cannot be shown | FR-06-11 |
| O3 | Object node | Radar status [Unavailable] | FR-06-11 (state value) |
| S3 | Action | Show each radar with its status and the time of its last data | FR-06-09 |
| D3 | Decision | Is any radar Offline? | FR-06-10 |
| S5 | Action | Highlight the Offline radars | FR-06-10 |
| M1 | Merge | (no text) | n/a |
| O1 | Object node | Radar status [Displayed] | FR-06-09 (state value) |
| A2 | Action | Review the status of the radars | FR-06-09 |
| M2 | Merge | (no text) | n/a |

Proposed FRs that are not yet in W1:

| L2 ID    | L2 function name                | EARS acceptance criterion                                                                                                                                                                          |
| :------- | :------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| FR-06-09 | Show radar status               | WHEN an admin requests the radar status, the system SHALL show every registered radar with its status (Online or Offline) and the time of its last data.                                           |
| FR-06-10 | Highlight Offline radars        | WHEN a radar is Offline, the system SHALL highlight it among the radars shown.                                                                                                                     |
| FR-06-11 | Report radar status unavailable | IF the system cannot obtain the radar status, or no radar is registered, THEN the system SHALL tell the admin that the status cannot be shown and SHALL NOT show an old status as the current one. |

## 3. Branches and acceptance tests

One row per outgoing branch of every decision. DBIZ3 turns each row into at least one test.

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| D1 \[No\] | Admin account whose role has no permission to monitor radar status | it requests the radar status | no radar status is shown, the request is rejected with an insufficient-permission message, and the Radar status is in state Rejected |
| D1 \[Yes\] → D2 \[No\] | the Admin has permission, and the Radar Gateway cannot be reached | the Admin requests the radar status | the message that the status cannot be shown is displayed, no old status is shown as current, and the Radar status is in state Unavailable |
| D1 \[Yes\] → D2 \[Yes\] → D3 \[Yes\] | the Admin has permission, the radar in room 101 sent data a few seconds ago, and the radar in room 102 sent no data for longer than the Offline limit | the Admin requests the radar status | both radars are shown with their status and last data time, the radar in room 102 is highlighted as Offline, and the Radar status is in state Displayed |
| D1 \[Yes\] → D2 \[Yes\] → D3 \[No\] | the Admin has permission and both registered radars sent data within the Offline limit | the Admin requests the radar status | both radars are shown with status Online and last data time, no radar is highlighted, and the Radar status is in state Displayed |

## 4. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Radar status | Displayed (after S3, with or without S5), Unavailable (after S4), Rejected (after S1) | Displayed, Unavailable, Rejected |

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Admin action | Screen ID |
| :---- | :---- |
| Request radar status |  |
| Review the status of the radars |  |

## 6. Open questions

| #   | Question                                                                                                                       | Blocking? | Default applied                                                             |
| :-- | :----------------------------------------------------------------------------------------------------------------------------- | :-------- | :-------------------------------------------------------------------------- |
| 1   | [NEEDS CLARIFICATION: after how long without data is a radar Offline?]                                                         | No        | 5 minutes. This value is an assumption; check it against the Radar Gateway. |
| 2   | [NEEDS CLARIFICATION: does the status refresh automatically while the Admin watches?]                                          | No        | No. The status is current when the Admin requests it or reloads the screen. |
| 3   | [NEEDS CLARIFICATION: is the Admin notified when a radar goes Offline?]                                                        | No        | No. This use case only shows the status.                                    |
| 4   | [NEEDS CLARIFICATION: should "no radar registered" and "Radar Gateway cannot be reached" be reported with different messages?] | No        | No. Both end at S4 with one message.                                        |


## 7. Verification log

| Check                                                               | Result         | By  | Date |
| :------------------------------------------------------------------ | :------------- | :-- | :--- |
| Renders on mermaid.live                                             | Pass           |     |      |
| Every decision has guards on all outgoing flows                     | Pass           |     |      |
| Every fork has a matching join                                      | Pass (no fork) |     |      |
| Every system action cites an FR                                     | Pass           |     |      |
| Every FR of UC-13 appears in the diagram                            | Pass (4 of 4)  |     |      |
| Lane names match the Context Diagram                                | Pass           |     |      |
| AI critic prompt run, findings resolved or logged as open questions | Pass           |     |      |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. The IDs FR-06-09 to FR-06-11 are proposed. Check W1 first: if UC-13 already has rows, reuse their IDs and wording instead of adding these.

| FR       | Function                        | Change                                                       |
| :------- | :------------------------------ | :----------------------------------------------------------- |
| FR-06-09 | Show radar status               | **New function (F-06, Must).** Covers A1, S2, S3, O1 and A2. |
| FR-06-10 | Highlight Offline radars        | **New function (F-06, Could).** Covers D3 and S5.           |
| FR-06-11 | Report radar status unavailable | **New function (F-06, Should).** Covers D2 and S4.           |
