# Activity Diagram: AD-UC-06 Send and acknowledge alert

| Field | Value |
|---|---|
| AD ID | `AD-UC-06` |
| Use case | `UC-06 Send and acknowledge alert` |
| Feature | `F-03 Abnormal Alert Handling` |
| Primary actor | Radar, Nurse|
| Lanes | Radar · Dien Hong System (our system, one lane) · Nurse |
| Inputs (L3-L5) | Context Diagram v[n] (Radar) · Use Case Diagram v[n] · Sequence: `SD-UC-06` · Architecture/ADR [n] · W1 rows FR-03-01 to FR-03-08. |
| Trigger | The Radar detects an abnormal event of a resident and sends it |
| Preconditions | Dien Hong System is running 24/7 and is connected to the radar feed (FR-03-08). Nurses are signed in (FR-00-01). |
| Success end state | One `Alert` in state `Closed` exists with the outcome recorded. |
| Failure end state(s) | The alert is still not acknowledged 1 minute after the third notice. The failure of this alert is logged and the alert stays `Escalated`. |
| FRs covered | `FR-03-01`, `FR-03-02`, `FR-03-03`, `FR-03-04`, `FR-03-05`, `FR-03-06` |
| Left out for now | `FR-03-07` (group repeated radar events) |
| Business rules applied | none |
| Version / status | v2.1 · Draft |
| Author | Nguyen Ha Linh Trang |

## 1. Diagram

Notation follows the DBIZ2 activity profile (`mermaid-activity-patterns.md`). Drawn left to right
(`LR`) to fit a landscape page. Change the first line to `swimlane-beta TB` for the course default.
Needs Mermaid 11.16 or later.

```mermaid
swimlane-beta LR
  subgraph RAD["Radar"]
    start@{ shape: sm-circ }
    r1("Detect abnormal event")
  end
  subgraph SYS["Dien Hong System"]
    s0("Receive radar event (FR-03-01)")
    s1("Store alert (FR-03-01)")
    o1["Alert [New]"]
    s2("Notify nurses, count 1 (FR-03-02)")
    m1{" "}
    t1@{ shape: hourglass }
    d1{" "}
    s4("Record acknowledgement (FR-03-03)")
    o2["Alert [Acknowledged]"]
    s6("Close alert (FR-03-06)")
    o4["Alert [Closed]"]
    d3{" "}
    s5("Notify nurses again, add 1 to count (FR-03-04)")
    o3["Alert [Escalated]"]
    s10("Log failed alert (FR-03-05)")
    stop1@{ shape: fr-circ }
  end
  subgraph NUR["Nurse"]
    n2("Handle and report outcome")
  end
  start --> r1 --> s0 --> s1 --> o1 --> s2 --> m1 --> t1
  t1 -->|"1 min after notice"| d1
  d1 -->|"[a nurse acknowledged]"| s4 --> o2 --> n2 --> s6 --> o4 --> stop1
  d1 -->|"[no acknowledgement]"| d3
  d3 -->|"[3 notices sent]"| s10 --> stop1
  d3 -->|"[fewer than 3 notices sent]"| s5 --> o3 --> m1
```

Reading notes:

- This diagram is the nurse side only. The notices to the Residents' Family are a separate use
  case, `UC-07 Receive alert notice`. UC-07 starts when the `Alert` reaches `New` (node `o1`) or
  `Closed` (node `o4`).
- After every notice the system **waits 1 minute** (time event `t1`) and then **checks** whether a
  nurse acknowledged (`d1`). The check happens on every round, also after a repeated notice, because
  the loop goes back through the merge `m1` to the wait.
- No acknowledgement and fewer than 3 notices sent: the system notifies the nurses again, adds 1 to
  the notice count and the alert is `Escalated`. No acknowledgement and 3 notices sent: the system
  logs the failure and the activity ends.
- A nurse acknowledges at any moment during the wait, and FR-03-03 sets the status at that moment.
  The diagram shows the system looking at the result after the minute, then recording it (`s4`).
- Decisions `d1` and `d3` have no question: their guards say it. The time expression is on the arrow
  going out of `t1`, as the profile asks.
- FR-03-05 is **Could**, FR-03-07 is **Should**, the rest are **Must**.. The diagram has 19 nodes.

## 2. Node trace

| Node | Kind | Text | Traces to |
|---|---|---|---|
| s0 | Action | Receive radar event | FR-03-01 |
| s1 | Action | Store alert (with resident, severity, location and time) | FR-03-01 |
| o1 | Object node | Alert [New] | FR-03-01 (state value) |
| s2 | Action | Notify nurses (online nurses on web, newest first), count 1 | FR-03-02 |
| t1 | Time event | 1 min after notice | FR-03-04 |
| d1 | Decision | A nurse acknowledged? | FR-03-03, FR-03-04 |
| s4 | Action | Record acknowledgement (and show nurse name) | FR-03-03 |
| o2 | Object node | Alert [Acknowledged] | FR-03-03 (state value) |
| n2 | Action (nurse lane) | Handle and report outcome | FR-03-06 |
| s6 | Action | Close alert (with outcome) | FR-03-06 |
| o4 | Object node | Alert [Closed] | FR-03-06 (state value) |
| d3 | Decision | 3 notices sent? | FR-03-04, FR-03-05 |
| s5 | Action | Notify nurses again, add 1 to count | FR-03-04 |
| o3 | Object node | Alert [Escalated] | FR-03-04 (state value) |
| s10 | Action | Log failed alert | FR-03-05 |

## 3. Branches and acceptance tests

Values marked [assumed] are defaults. The team confirms them with the client.

| Branch | Given | When | Then |
|---|---|---|---|
| d1 [a nurse acknowledged] | The first notice was sent to nurses at 14:32:00 and a nurse acknowledged it at 14:32:40 | 14:33:00 arrives (1 min after the notice) | The system sees the acknowledgement, records it and the alert is set to Acknowledged |
| d1 [no acknowledgement], d3 [fewer than 3 notices sent] | The first notice was sent at 14:32:00 (count 1) and nobody acknowledges | 14:33:00 arrives | Status becomes Escalated, the nurses get the second notice and the count increases to 2 |
| d1 [no acknowledgement], d3 [fewer than 3 notices sent] | The second notice was sent at 14:33:00 and a nurse acknowledges at 14:33:30 | 14:34:00 arrives | The system sees the acknowledgement, stores it, and the alert is set to Acknowledged |
| d3 [3 notices sent] | Notices were sent at 14:32:00, 14:33:00 and 14:34:00 and nobody acknowledges | 14:35:00 arrives (1 min after the third notice) | The failure of this alert is logged. No more notices are sent. |

## 4. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
|---|---|---|
| Alert | New, Acknowledged, Escalated, Closed | New, Acknowledged, Escalated, Closed |

State changes of the Alert. These names become the allowed values of `alert.status` in the data
model, so keep the spelling identical everywhere.

| From | Event | FR | To |
|---|---|---|---|
| (none) | Radar event is stored | FR-03-01 | New |
| New | Nobody acknowledges within 1 min and fewer than 3 notices were sent | FR-03-04 | Escalated |
| Escalated | Same again | FR-03-04 | Escalated |
| Escalated | Nobody acknowledges within 1 min after the third notice | FR-03-05 | Escalated (failure logged) |
| New | A nurse acknowledges | FR-03-03 | Acknowledged |
| Escalated | A nurse acknowledges | FR-03-03 | Acknowledged |
| Acknowledged | The nurse reports the outcome | FR-03-06 | Closed |

The alert also needs to remember how many notices were sent to nurses (for `d3`). `s2` sets the count to 1 and `s5` adds 1.

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
|---|---|
| | |

## 6. Open questions

| # | Question | Blocking? | Default applied |
|---|---|---|---|
| 1 | [NEEDS CLARIFICATION: Is the 1 minute the same for every severity?] | Yes | 1 min for all |
| 2 | [NEEDS CLARIFICATION: After the failure is logged, what happens to the alert? Does it stay open on the nurses' list, and does anybody (Admin, manager) get told?] | Yes | The alert stays open on the nurses' list |
| 3 | [NEEDS CLARIFICATION: Does "3 notices" include the first notice?] | Yes | Yes: the first notice and two more |
| 4 | [NEEDS CLARIFICATION: Does the AI in our app decide that an event is abnormal, or does the radar device?] | Yes | The radar device decides |
| 5 | [NEEDS CLARIFICATION: Are the four status names New, Acknowledged, Escalated, Closed the ones the client wants?] | No | These four |
| 6 | [NEEDS CLARIFICATION: Does a nurse see all alerts or only those of her own area?] | No | All online nurses |
| 7 | [NEEDS CLARIFICATION: If the app is down for a moment, does the radar keep the events and send them later, or are they lost?] | Yes | Radar keeps events and resends |

## 7. Verification log

| Check | Result | By | Date |
|---|---|---|---|
| Renders on mermaid.live | Syntax parses with Mermaid 11.17.2 (`LR` and `flowchart LR` fallback). Open it once on mermaid.live to check the layout | | |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass | | |
| No action has two incoming flows (merge used instead) | Pass (`m1` is the only merge; `stop1` is a final node) | | |
| Every fork has a matching join | Pass (no fork in this diagram) | | |
| Every system action cites an FR | Pass (7 of 7) | | |
| Every FR of this use case appears in the diagram | 6 of 7. FR-03-07 is left out on purpose for now | | |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | Pass. System name "Dien Hong System" | | |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check | | |
| AI critic prompt run, findings resolved or logged as open questions | To run | | |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes this diagram needs in the
function table.

| FR | Function | Change |
|---|---|---|
| FR-03-01 | Receive abnormal alert from radar | **Change**: store the resident too and set status New. Needs room and resident data from F-01 |
| FR-03-02 | Receive alert (nurse) | **Change** the UC column: the old UC-07 (Receive alert, web) becomes UC-06 |
| FR-03-03 | Acknowledge alert | **New**, Must: "WHEN a nurse acknowledges an alert, the system SHALL set its status to Acknowledged and show the nurse name to the other nurses" |
| FR-03-04 | Notify nurses again when nobody acknowledges | **New**, Must: "IF 1 minute after a notice the alert is still not acknowledged and fewer than 3 notices were sent, the system SHALL set its status to Escalated and notify the nurses again" |
| FR-03-05 | Log failed alert | **New**, Must: "IF an alert is still not acknowledged 1 minute after the third notice, the system SHALL log the failure of that alert" |
| FR-03-06 | Close alert with outcome | **New**, Must: "WHEN a nurse reports the outcome of an alert, the system SHALL record it and set the status to Closed" |
| FR-03-07 | Group repeated radar events | **New**, Should. Not in this version of the diagram |
| FR-03-09 | Receive alert notice (family), the old FR-03-03 | **Moves to UC-07** and is renumbered (see `AD-UC-07.md`) |
| FR-03-08 | Always on | **New**, Must: "The system SHALL run 24/7 and keep the connection to the radar feed" |
