# Activity Diagram: AD-UC-07 Receive alert notice

| Field | Value |
|---|---|
| AD ID | `AD-UC-07` |
| Use case | `UC-07 Receive alert notice` |
| Feature | `F-03 Abnormal Alert Handling` |
| Primary actor | Residents' Family (they receive the notice; the flow starts from an alert state change in UC-06) |
| Lanes | Radar · Dien Hong System (our system, one lane) · Residents' Family |
| Inputs (L3-L5) | Context Diagram v[n] (Radar, Residents' Family) · Use Case Diagram v[n] (UC-07 added) · Sequence: `SD-UC-07` · Architecture/ADR [n] · W1 rows FR-03-09 to FR-03-11 · `AD-UC-[...]` |
| Trigger | An alert reaches the state `New` or the state `Closed` (see `AD-UC-06`) |
| Preconditions | none (a resident without linked family is handled by FR-03-11) |
| Success end state | A dangerous alert: the family got one first notice and, when the alert was closed, one closed notice. A not dangerous alert: the family got nothing. |
| Failure end state(s) | A dangerous alert whose resident has no linked family member: the push is skipped. A dangerous alert whose first push failed after [n] retries: the failure is logged. In b[...]
| FRs covered | `FR-03-09, FR-03-10, FR-03-11` |
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
    s11("Skip push (FR-03-11)")
    m1{" "}
    s3("Send first notice to family, once per alert (FR-03-09)")
    d6{" "}
    s13("Retry push, add 1 to retry count (FR-03-11)")
    s12("Log failed push (FR-03-11)")
    o1["Family notice [First sent]"]
    s8("Receive alert closed (from UC-06)")
    s9("Send closed notice to family, once (FR-03-10)")
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

- This diagram is the family side only. The nurse side is `UC-06 Send and acknowledge alert`. The two use cases
  meet through the state of the `Alert`, they are not drawn in one diagram.
- There is one start, in the Radar lane: the Radar detects an abnormal event and the app receives it
  (`s0`, FR-03-01), which makes a `New` alert (stored in `AD-UC-06`).
- Only a dangerous alert gets a first notice, and only **once**. After that the flow waits until the
  alert reaches `Closed` in `AD-UC-06` (`s8`), then the family gets **one more** notice. A not
  dangerous alert ends right after `d2`, so it never gets a closed notice.
- The other states (`Acknowledged`, `Escalated`) do not send anything to the family. Notifying the
  nurses again does not send another notice to the family.
- New in v1.3 (FR-03-11): for a dangerous alert, `d5` first checks whether the resident has a linked
  family member. If not, the system skips the push (`s11`) and the activity ends. If yes, the system
  sends the first notice (`s3`) and `d6` checks the result. A failed push is retried: `s13` adds 1 to
  the retry count and goes back through the merge `m1` to `s3`. The retry count starts at 0 when the
  alert reaches `s3` the first time. After [n] retries the failure is logged (`s12`) and the activity
  ends. The object `Family notice [First sent]` is reached only when the push is delivered, so the
  closed notice (FR-03-10) is not sent after a skipped or failed first notice.
- The rule of FR-03-11 is drawn for the first notice only. Whether it also applies to the closed
  notice is open question 6.
- Diamonds `d2`, `d5` and `d6` have no question: their guards say it.
- FR-03-09 is **Must**; FR-03-10 and FR-03-11 are **Could**. The diagram has 18 nodes.

## 2. Node trace

| Node | Kind | Text | Traces to |
|---|---|---|---|
| r1 | Action (radar lane) | Detect abnormal event | FR-03-01 (see AD-UC-06) |
| s0 | Action | Receive radar event | FR-03-01 (see AD-UC-06) |
| s8 | Action | Receive alert closed (from UC-06) | FR-03-10 |
| d2 | Decision | Severity dangerous? | FR-03-09, BR-03-01 |
| d5 | Decision | Resident has a linked family member? | FR-03-11 |
| s11 | Action | Skip push | FR-03-11 |
| m1 | Merge | First send or retry | FR-03-11 |
| s3 | Action | Send first notice to family, once per alert | FR-03-09 |
| d6 | Decision | Push delivered? | FR-03-11 |
| s13 | Action | Retry push, add 1 to retry count | FR-03-11 |
| s12 | Action | Log failed push | FR-03-11 |
| o1 | Object node | Family notice [First sent] | FR-03-09 (state value) |
| s9 | Action | Send closed notice to family, once | FR-03-10 |
| o2 | Object node | Family notice [Closed sent] | FR-03-10 (state value) |

## 3. Branches and acceptance tests

Values marked [assumed] are defaults. The team confirms them with the client.

| Branch | Given | When | Then |
|---|---|---|---|
| s0 → d2 [severity dangerous] | A new alert with severity High for the resident of room 204, who has 2 linked family members | The alert is stored with status New | Both family members receive one first notice |
| s0 → d2 [not dangerous] | A new alert with severity Low | The alert is stored with status New | The family receives nothing |
| d5 [no linked family member] | A new alert with severity High for a resident who has no linked family member | The alert is stored with status New | The push is skipped, nothing is sent and the failure is logged or noted according to FR-03-11 |
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
| (none) | Alert is New and dangerous, first notice delivered | FR-03-09 | First sent |
| First sent | Alert is Closed, closed notice sent | FR-03-10 | Closed sent |

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
| 6 | [NEEDS CLARIFICATION: Do the skip and retry rules of FR-03-11 also apply to the closed notice?] | No | Yes, same rules (not drawn separately) |
| 7 | [NEEDS CLARIFICATION: If the resident has several linked members and the push fails for only one, is it retried for that member only?] | No | Yes, only for the member whose push failed |
| 8 | [NEEDS CLARIFICATION: Where is the failed push logged, and does the failed-push log appear in UC-13 View access and action logs?] | No | In the same log as the failed alert of FR-03-05 |

## 7. Verification log

| Check | Result | By | Date |
|---|---|---|---|
| Renders on mermaid.live | Syntax parses with Mermaid 11.17.2 (`LR` and `flowchart LR` fallback). Open it once on mermaid.live to check the layout | | |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass | | |
| No action has two incoming flows (merge used instead) | Pass (`m1` is the only merge; `stop1` is a final node with four incoming flows) | | |
| Every fork has a matching join | Pass (no fork in this diagram) | | |
| Every system action cites an FR | Pass (7 of 7: `s0` FR-03-01, `s8` FR-03-10; `s3` FR-03-09, `s9` FR-03-10, `s11`, `s12`, `s13` FR-03-11) | | |
| Every FR of this use case appears in the diagram | 3 of 3 | | |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | To check: system name "Dien Hong System" | | |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check | | |
| AI critic prompt run, findings resolved or logged as open questions | To run | | |

## Notes for the integrator: changes needed in W1 and the use case list

| Item | Change |
|---|---|
| Use case inventory (W0 Table 1) | UC-07 is in W0 Table 1 and the Use Case Diagram (Must, F-03) |
| Priority | Resolved: UC-07 is Must because FR-03-09 is Must (W4) |
| FR-03-09 | Receive alert notice (family), Must. EARS: “WHEN a new alert is dangerous (BR-03-01: severity High) and its resident has at least one linked family member, the system SHALL send one first notice to every linked family account as a push notice to their phone.” ([...] |
| FR-03-10 | **New**, Send closed notice to family, Could: "WHEN an alert whose first notice was delivered is closed, the system SHALL send one closed notice with the outcome to the linked family accounts." |
| FR-03-11 | **New**, Skip or retry family push, Could: “IF the resident of a dangerous alert has no linked family member, THEN the system SHALL skip the push. IF a push to the family fails, THEN the system SHALL retry it up to 3 times and then log the failure.”. Now in the diagram (`d5`, `s11`, `s13`, `s12`) |
| BR-03-01 | **New**: an alert is dangerous when its severity is High |

















































n/a
