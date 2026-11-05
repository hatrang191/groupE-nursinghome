# Activity Diagram: AD-UC-06 Receive alert

| Field | Value |
|---|---|
| AD ID | `AD-UC-06` |
| Use case | `UC-06 Receive alert` |
| Feature | `F-03 Abnormal Alert Handling` |
| Primary actor | Nurse (the flow starts from an event sent by the Radar) |
| Lanes | Radar · Dien Hong System (our system, one lane) · Nurse |
| Inputs (L3-L5) | Context Diagram v[n] (Radar) · Use Case Diagram v[n] · Sequence: `SD-UC-06` · Architecture/ADR [n] · W1 rows FR-03-01 to FR-03-06 |
| Trigger | The Radar detects an abnormal event of a resident and the app receives it |
| Preconditions | Dien Hong System is running 24/7 and is connected to the radar feed (NFR-01). Nurses are logged in on the web. |
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
- All FRs of this use case are **Must**. The diagram has 19 nodes.

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
| FR-03-02 | Receive alert (web) | **Change** the UC column: the old UC-07 (Receive alert, web) becomes UC-06 |
| FR-03-03 | Acknowledge alert | **New**, Must: "WHEN a nurse acknowledges an alert, the system SHALL set its status to Acknowledged and show the nurse name to the other nurses" |
| FR-03-04 | Notify nurses again when nobody acknowledges | **New**, Must: "IF 1 minute after a notice the alert is still not acknowledged and fewer than 3 notices were sent, the system SHALL set its status to Escalated and notify the nurses again" |
| FR-03-05 | Log failed alert | **New**, Must: "IF an alert is still not acknowledged 1 minute after the third notice, the system SHALL log the failure of that alert" |
| FR-03-06 | Close alert with outcome | **New**, Must: "WHEN a nurse reports the outcome of an alert, the system SHALL record it and set the status to Closed" |
| FR-03-07 | Group repeated radar events | **New**, Should. Not in this version of the diagram |
| FR-03-08 | Receive alert (mobile app), the old FR-03-03 | **Moves to UC-07** and is renumbered (see `AD-UC-07.md`) |
| NFR-01 | Always on | **New**, Must: "The system SHALL run 24/7 and keep the connection to the radar feed" |
| FR-06-[n] | Radar feed lost | **New**, Must: "WHEN the connection to the radar feed is lost, the system SHALL notify the Admin". This is a time-trigger flow, so UC-12 (Monitor radar status) the system should notify the admin. |

Also check that F-06 (UC-13 View access and action logs) has a logging FR that includes alert
actions and the failed-alert log of FR-03-05.


# Activity Diagram: AD-UC-07 Receive alert notice

| Field | Value |
|---|---|
| AD ID | `AD-UC-07` |
| Use case | `UC-07 Receive alert notice` |
| Feature | `F-03 Abnormal Alert Handling` |
| Primary actor | Residents' Family (they receive the notice; the flow starts from an alert state change in UC-06) |
| Lanes | Radar · Dien Hong System (our system, one lane) · Residents' Family |
| Inputs (L3-L5) | Context Diagram v[n] (Radar, Residents' Family) · Use Case Diagram v[n] (UC-07 added) · Sequence: `SD-UC-07` · Architecture/ADR [n] · W1 rows FR-03-08 to FR-03-10 · `AD-UC-[...]` |
| Trigger | An alert reaches the state `New` or the state `Closed` (see `AD-UC-06`) |
| Preconditions | none (a resident without linked family is handled by FR-03-10) |
| Success end state | A dangerous alert: the family got one first notice and, when the alert was closed, one closed notice. A not dangerous alert: the family got nothing. |
| Failure end state(s) | A dangerous alert whose resident has no linked family member: the push is skipped. A dangerous alert whose first push failed after [n] retries: the failure is logged. In b[...]
| FRs covered | `FR-03-08`, `FR-03-09`, `FR-03-10` |
| Left out for now | none |
| Business rules applied | `BR-03-01` An alert is dangerous when its severity is High [assumed, ask the client] |
| Version / status | v1.3 · Draft |
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
    d2{" "}
    d5{" "}
    s11("Skip push (FR-03-10)")
    m1{" "}
    s3("Send first notice to family, once per alert (FR-03-08)")
    d6{" "}
    s13("Retry push, add 1 to retry count (FR-03-10)")
    s12("Log failed push (FR-03-10)")
    o1["Family notice [First sent]"]
    s8("Receive alert closed (from UC-06)")
    s9("Send closed notice to family, once (FR-03-09)")
    o2["Family notice [Closed sent]"]
    stop1@{ shape: fr-circ }
  end
  subgraph FAM["Residents' Family"]
    fam1("Receive first notice")
    fam2("Receive closed notice")
  end
  start --> r1 --> s0 --> d2
  d2 -->|"[not dangerous]"| stop1
  d2 -->|"[severity dangerous]"| d5
  d5 -->|"[no linked family member]"| s11 --> stop1
  d5 -->|"[has linked family member]"| m1 --> s3 --> d6
  d6 -->|"[push delivered]"| o1 --> fam1 --> s8 --> s9 --> o2 --> fam2 --> stop1
  d6 -->|"[push failed, fewer than n retries]"| s13 --> m1
  d6 -->|"[push failed, n retries done]"| s12 --> stop1
```

Reading notes:

- This diagram is the family side only. The nurse side is `UC-06 Receive alert`. The two use cases
  meet through the state of the `Alert`, they are not drawn in one diagram.
- There is one start, in the Radar lane: the Radar detects an abnormal event and the app receives it
  (`s0`, FR-03-01), which makes a `New` alert (stored in `AD-UC-06`).
- Only a dangerous alert gets a first notice, and only **once**. After that the flow waits until the
  alert reaches `Closed` in `AD-UC-06` (`s8`), then the family gets **one more** notice. A not
  dangerous alert ends right after `d2`, so it never gets a closed notice.
- The other states (`Acknowledged`, `Escalated`) do not send anything to the family. Notifying the
  nurses again does not send another notice to the family.
- New in v1.3 (FR-03-10): for a dangerous alert, `d5` first checks whether the resident has a linked
  family member. If not, the system skips the push (`s11`) and the activity ends. If yes, the system
  sends the first notice (`s3`) and `d6` checks the result. A failed push is retried: `s13` adds 1 to
  the retry count and goes back through the merge `m1` to `s3`. The retry count starts at 0 when the
  alert reaches `s3` the first time. After [n] retries the failure is logged (`s12`) and the activity
  ends. The object `Family notice [First sent]` is reached only when the push is delivered, so the
  closed notice (FR-03-09) is not sent after a skipped or failed first notice.
- The rule of FR-03-10 is drawn for the first notice only. Whether it also applies to the closed
  notice is open question 6.
- Diamonds `d2`, `d5` and `d6` have no question: their guards say it.
- FR-03-08 to FR-03-10 are all **Should**. The diagram has 18 nodes.

## 2. Node trace

| Node | Kind | Text | Traces to |
|---|---|---|---|
| r1 | Action (radar lane) | Detect abnormal event | FR-03-01 (see AD-UC-06) |
| s0 | Action | Receive radar event | FR-03-01 (see AD-UC-06) |
| s8 | Action | Receive alert closed (from UC-06) | FR-03-09 |
| d2 | Decision | Severity dangerous? | FR-03-08, BR-03-01 |
| d5 | Decision | Resident has a linked family member? | FR-03-10 |
| s11 | Action | Skip push | FR-03-10 |
| m1 | Merge | First send or retry | FR-03-10 |
| s3 | Action | Send first notice to family, once per alert | FR-03-08 |
| d6 | Decision | Push delivered? | FR-03-10 |
| s13 | Action | Retry push, add 1 to retry count | FR-03-10 |
| s12 | Action | Log failed push | FR-03-10 |
| o1 | Object node | Family notice [First sent] | FR-03-08 (state value) |
| s9 | Action | Send closed notice to family, once | FR-03-09 |
| o2 | Object node | Family notice [Closed sent] | FR-03-09 (state value) |

## 3. Branches and acceptance tests

Values marked [assumed] are defaults. The team confirms them with the client.

| Branch | Given | When | Then |
|---|---|---|---|
| s0 → d2 [severity dangerous] | A new alert with severity High for the resident of room 204, who has 2 linked family members | The alert is stored with status New | Both family members receive one first notice |
| s0 → d2 [not dangerous] | A new alert with severity Low | The alert is stored with status New | The family receives nothing |
| d5 [no linked family member] | A new alert with severity High for a resident who has no linked family member | The alert is stored with status New | The push is skipped, nothing is sent and the failure is logged or noted according to FR-03-10 |
| d6 [push failed, fewer than n retries] | A first notice was sent and the push failed (retry count 0) | The system sees the failure | The system retries, the retry count becomes 1 and the first notice is retried |
| d6 [push failed, n retries done] | The push failed and the system already retried n times | The system sees the failure | The failed push is logged and the activity ends. No closed notice is sent |
| o1 → s8 → s9 | The alert with severity High had a first notice | The alert status becomes Closed with outcome "false alarm" | Both family members receive one closed notice showing the outcome |
| d2 [not dangerous] → end | The alert with severity Low had no first notice | The alert status becomes Closed | The family receives nothing [assumed, see question 3] |

## 4. Object states used

| Object | States that appear in this diagram | Full state list (all diagrams) |
|---|---|---|
| Family notice | First sent, Closed sent | First sent, Closed sent |
| Alert (read only, written in `AD-UC-06`) | New, Closed | New, Acknowledged, Escalated, Closed |

State changes of the Family notice. These names become the allowed values of the status of a
family notice in the data model.

| From | Event | FR | To |
|---|---|---|---|
| (none) | Alert is New and dangerous, first notice delivered | FR-03-08 | First sent |
| First sent | Alert is Closed, closed notice sent | FR-03-09 | Closed sent |

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
|---|---|
| | |

## 6. Open questions

| # | Question | Blocking? | Default applied |
|---|---|---|---|
| 1 | [NEEDS CLARIFICATION: Which severity levels count as dangerous (BR-03-01)? Does the radar give levels such as Low, Medium, High?] | Yes | Only High |
| 2 | [NEEDS CLARIFICATION: Does the closed notice show the outcome (real event or false alarm)?] | No | Yes |
| 3 | [NEEDS CLARIFICATION: When an alert was not dangerous, does the family get a closed notice anyway?] | No | No, only if the first notice was sent |
| 4 | [NEEDS CLARIFICATION: Which family members get the notice: all linked members or only the main contact?] | No | All linked members |
| 5 | [NEEDS CLARIFICATION: How many retries is [n], and how long does the system wait between two retries?] | Yes | [n] = 3, 1 minute between retries [assumed] |
| 6 | [NEEDS CLARIFICATION: Do the skip and retry rules of FR-03-10 also apply to the closed notice?] | No | Yes, same rules (not drawn separately) |
| 7 | [NEEDS CLARIFICATION: If the resident has several linked members and the push fails for only one, is it retried for that member only?] | No | Yes, only for the member whose push failed |
| 8 | [NEEDS CLARIFICATION: Where is the failed push logged, and does the failed-push log appear in UC-13 View access and action logs?] | No | In the same log as the failed alert of FR-03-05 |

## 7. Verification log

| Check | Result | By | Date |
|---|---|---|---|
| Renders on mermaid.live | Syntax parses with Mermaid 11.17.2 (`LR` and `flowchart LR` fallback). Open it once on mermaid.live to check the layout | | |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass | | |
| No action has two incoming flows (merge used instead) | Pass (`m1` is the only merge; `stop1` is a final node with four incoming flows) | | |
| Every fork has a matching join | Pass (no fork in this diagram) | | |
| Every system action cites an FR | Pass (7 of 7: `s0` FR-03-01, `s8` FR-03-09, `s3`, `s9`, `s11`, `s12`, `s13`) | | |
| Every FR of this use case appears in the diagram | 3 of 3 | | |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | To check: system name "Dien Hong System" | | |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check | | |
| AI critic prompt run, findings resolved or logged as open questions | To run | | |

## Notes for the integrator: changes needed in W1 and the use case list

| Item | Change |
|---|---|
| Use case inventory (W0 Table 1) | **Add** `UC-07 Receive alert notice`, primary actor Residents' Family, feature F-03. Add it to the Use Case Diagram as well |
| Priority | Both FRs are Should, so by the course rule the use case is Should and gets "later". Either set the priority of UC-07 to Must, or keep this diagram as an early draft |
| FR-03-08 | **Renumber** the old FR-03-03 to FR-03-08, change its UC column (UC-08 in the old list) to UC-07, and change the sentence: "WHEN a new alert is stored and its severity is dangerous ([...] |
| FR-03-09 | **New**, Should (numbered after FR-03-08): "WHEN an alert is set to Closed and its first notice was sent to the family, the system SHALL send one push notice to the family members of the resident" |
| FR-03-10 | **New**, Should: "IF a resident has no linked family member, the system SHALL skip the push. IF a push fails, the system SHALL retry [n] times and log it". Now in the diagram (`d5`, `s11`, `s13`, `s12`) |
| BR-03-01 | **New**: an alert is dangerous when its severity is High |






















































































































































































































































































































































































n/a







































































































































































n/a




















n/a















