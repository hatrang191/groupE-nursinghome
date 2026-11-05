# AD-UC-05

# AD-UC-05

# Activity Diagram: AD-UC-05 Update condition

| Field | Value |
| --- | --- |
| AD ID | `AD-UC-05` |
| Use case | `UC-05 Update condition` |
| Feature | `F-02 Condition Tracking` |
| Primary actor | Nurse |
| Lanes | Nurse · Dien Hong system |
| Inputs (L3-L5) | Context Diagram (Nurses ↔︎ Dien Hong system: Daily status updates, shift handover notes, updates for family) · Use Case Diagram (UC-05, Nurse) · W1 rows FR-02-01-FE, FR-02-01-BE “Validate condition”, FR-02-02-FE “Correct condition”, FR-02-03-FE, FR-02-03-BE “Update condition”, FR-02-04-BE “Keep condition history” |
| Trigger | The nurse selects or enters a resident’s condition and saves it (FR-02-03) |
| Preconditions | The nurse is signed in and their role allows updating conditions (FR-06-03). The resident already has a profile (UC-01). |
| Success end state | One new condition record, in state Saved, is stored for the resident with the time and the nurse’s name. Every earlier condition record of that resident is unchanged. |
| Failure end state(s) | No condition record is saved while the condition is invalid and earlier records stay unchanged. The nurse is told why and may correct the condition and save again (FR-02-01, FR-02-02). |
| FRs covered | `FR-02-01` , `FR-02-02` , `FR-02-03` , `FR-02-04` |
| Business rules applied | None (W1 has no BR rows) |
| Version / status | v0.1 · Draft |
| Author | Nguyen Tran Khanh Linh |

## 1. Diagram

Notation follows the DBIZ2 activity profile (`mermaid-activity-patterns.md`). Rendered with `swimlane-beta`, which needs Mermaid 11.16 or later.

```mermaid
swimlane-beta TB
  subgraph NUR["Nurse"]
    start@{ shape: sm-circ }
    a1("Select resident")
    a2("Select or enter resident condition and save")
    a3("Correct condition")
    stop1@{ shape: fr-circ }
  end
  subgraph SYS["Dien Hong system"]
    m1{" "}
    d1{"Condition valid?"}
    s0("Reject the save, leave earlier records unchanged and tell the nurse why (FR-02-01)")
    s3("Let the nurse correct the condition and save it again (FR-02-02)")
    s1("Store condition with time and nurse name (FR-02-03)")
    s2("Keep it as a new record, earlier records unchanged (FR-02-04)")
    o1["Resident Condition Record [Saved]"]
  end
  start --> a1 --> m1 --> a2 --> d1
  d1 -->|"[no]"| s0 --> s3 --> a3 --> m1
  d1 -->|"[yes]"| s1 --> s2 --> o1 --> stop1
```

Reading notes:

- `m1` is a merge node. After a rejected save the flow returns through it, never straight into `a2`: an action with two incoming control flows would wait for both (OMG UML 2.5.1, sub clause 15.2.3.2).
- Validation and correction are their own functions: FR-02-01 “Validate condition” and FR-02-02 “Correct condition”. `d1` and `s0` trace to FR-02-01, `s3` traces to FR-02-02, and `a3` is the nurse’s side of `s3`. The existing Update condition and Keep condition history become FR-02-03 and FR-02-04.
- `o1` shows the stored result of the use case, a condition record in state Saved. It is not a message to the nurse.

## 2. Node trace

| Node | Kind | Text | Traces to |
| --- | --- | --- | --- |
| d1 | Decision | Condition valid? | FR-02-01 (IF clause) |
| s0 | Action | Reject the save, leave earlier records unchanged and tell the nurse why | FR-02-01 |
| s3 | Action | Let the nurse correct the condition and save it again | FR-02-02 |
| s1 | Action | Store condition with time and nurse name | FR-02-03 |
| s2 | Action | Keep it as a new record, earlier records unchanged | FR-02-04 |
| o1 | Object node | Resident Condition Record [Saved] | FR-02-04 (state value `Saved`) |

## 3. Branches and acceptance tests

The condition values below are examples. W1 does not list the allowed conditions.

| Branch | Given | When | Then |
| --- | --- | --- | --- |
| d1 [yes] | the nurse has selected resident “Nguyễn Văn An” and entered the condition “Stable” | the nurse saves at 08:00 | one condition record in state Saved is stored for the resident with the time 08:00 and the nurse’s name |
| d1 [yes] (history kept) | the resident already has the 08:00 record “Stable” | the nurse saves “Fever” at 14:00 | the resident has two records; the 08:00 record is unchanged (FR-02-04) |
| d1 [no] | the nurse has selected the resident but left the condition empty | the nurse saves | no condition record is stored, earlier records are unchanged, and the nurse is told why the condition was rejected |
| d1 [no] → d1 [yes] | a save was rejected because the condition was empty | the nurse enters “Stable” and saves again | exactly one condition record is stored |

## 4. Object states used

| Object | States in this diagram | Full state list (all diagrams) |
| --- | --- | --- |
| Resident Condition Record | Saved (written) | Saved |

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
| --- | --- |
|  |  |

## 6. Open questions

| # | Question | Blocking? | Default applied |
| --- | --- | --- | --- |
| 1 | [NEEDS CLARIFICATION: what makes a condition invalid, what must the system do then, and is the nurse told why? FR-02-03 only describes a successful save.] (nodes `d1`, `s0`) | Yes | Tests use “condition left empty” as the invalid case; nothing is saved. |
| 2 | [NEEDS CLARIFICATION: may the nurse correct the condition and submit it again after a rejected save?] (nodes `a3`, `n2`) | No | The nurse may correct and save again. Nothing is saved in between. |

## 7. Verification log

| Check | Result | By | Date |
| --- | --- | --- | --- |
| Renders on mermaid.live | Pass on a local mermaid-cli render; recheck on mermaid.live |  |  |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass (`d1`: [yes] / [no]) |  |  |
| No action has two incoming flows (merge used instead) | Pass (`m1` receives `a1` and `a3`; `stop1` has one incoming flow) |  |  |
| Every fork has a matching join | N/A (no fork) |  |  |
| Every system action cites an FR | Pass for `s0`, `s1`, `s2` and `s3`; the object node `o1` cites FR-02-04. `d1` cites FR-02-01 (IF clause). |  |  |
| Every FR of this use case appears in the diagram | Pass (4 of 4: FR-02-01, FR-02-02, FR-02-03, FR-02-04) |  |  |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | Pass (“Nurse” from the Use Case Diagram, “Dien Hong system” from the Context Diagram) |  |  |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check |  |  |
| AI critic prompt run, findings resolved or logged as open questions | Run once in this chat on an earlier version of this diagram; findings logged as open questions #1 and #2. Re-run on this version before review. |  |  |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes the UC-05 diagram needs in the function table.

| FR | Function | Change |
| --- | --- | --- |
| FR-02-01 (new ID) | Validate condition | **New function answering open question #1:** “IF the condition is invalid, THEN the system SHALL reject the save, leave earlier records unchanged, and tell the nurse why.” This covers `d1` and `s0`. |
| FR-02-02 (new ID) | Correct condition | **New function answering open question #2:** “IF the condition is invalid, THEN the system SHALL let the nurse correct the condition and save it again.” This covers `s3` and `a3`. If the answer is no, delete both nodes and the loop through `m1`.  |
| FR-02-03 (previously FR-02-01) | Update condition | **Change** the EARS criterion: “WHEN a nurse selects an existing resident, enters the resident’s condition and saves, the system SHALL store it for that resident with the time and the nurse’s name”. This covers `a1`. |
| FR-02-04 (previously FR-02-02) | Keep condition history | None to the function table. |

---

Template source: DBIZ2 Lesson 7, VJCBI College - FTU. Notation: OMG UML 2.5.1, Clause 15.