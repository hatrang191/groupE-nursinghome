# AD-UC-05

# Activity Diagram: AD-UC-05 Update condition

| Field | Value |
| --- | --- |
| AD ID | `AD-UC-05` |
| Use case | `UC-05 Update condition` |
| Feature | `F-02 Condition Tracking` |
| Primary actor | Nurse |
| Lanes | Nurse · Dien Hong system |
| Inputs (L3-L5) | Context Diagram (Nurses ↔︎ Dien Hong system: Daily status updates, shift handover notes, updates for family) · Use Case Diagram (UC-05, Nurse) · W1 rows FR-02-01-FE, FR-02-01-BE, FR-02-02-BE |
| Trigger | The nurse selects or enters a resident’s condition and saves it (FR-02-01) |
| Preconditions | The nurse is signed in and their role allows updating conditions (FR-06-03). The resident already has a profile (UC-01). |
| Success end state | One new condition record, in state Saved, is stored for the resident with the time and the nurse’s name. Every earlier condition record of that resident is unchanged. |
| Failure end state(s) | No condition record is saved while the condition is invalid and earlier records stay unchanged. What the nurse sees, and whether the nurse may correct and save again, is open (section 6, #1 and #2). |
| FRs covered | `FR-02-01`, `FR-02-02` |
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
    s0("[NEEDS CLARIFICATION: what makes a condition invalid, what must the system do then, and is the nurse told why?]")
    n2("[NEEDS CLARIFICATION: may the nurse correct the condition and submit it again?]")
    s1("Store condition with time and nurse name (FR-02-01)")
    s2("Keep it as a new record, earlier records unchanged (FR-02-02)")
    o1["Resident Condition Record [Saved]"]
  end
  start --> a1 --> m1 --> a2 --> d1
  d1 -->|"[no]"| s0 --> n2 --> a3 --> m1
  d1 -->|"[yes]"| s1 --> s2 --> o1 --> stop1
```

Reading notes:

- `m1` is a merge node. After a rejected save the flow returns through it, never straight into `a2`: an action with two incoming control flows would wait for both (OMG UML 2.5.1, sub clause 15.2.3.2).
- The two `[NEEDS CLARIFICATION]` nodes (`s0`, `n2`) mark behaviour that no FR of UC-05 defines. They are open questions, not requirements. `a3` is a Nurse-lane step with no FR and sits behind them (`s0` and `n2`). Delete it and the loop through `m1` if the team does not add FRs for it.
- `d1` has no FR behind it: FR-02-01 and FR-02-02 contain no IF clause. The decision exists only because `s0` needs somewhere to hang from.
- `o1` shows the stored result of the use case, a condition record in state Saved. It is not a message to the nurse.

## 2. Node trace

| Node | Kind | Text | Traces to |
| --- | --- | --- | --- |
| d1 | Decision | Condition valid? | none: no IF clause in W1 (open question #1) |
| s1 | Action | Store condition with time and nurse name | FR-02-01 |
| s2 | Action | Keep it as a new record, earlier records unchanged | FR-02-02 |
| s0 | Open question | What makes a condition invalid, and what happens then? | none (open question #1) |
| n2 | Open question | May the nurse correct and submit again? | none (open question #2) |
| o1 | Object node | Resident Condition Record [Saved] | FR-02-02 (state value `Saved`) |

## 3. Branches and acceptance tests

The condition values below are examples. W1 does not list the allowed conditions.

| Branch | Given | When | Then |
| --- | --- | --- | --- |
| d1 [yes] | the nurse has selected resident “Nguyễn Văn An” and entered the condition “Stable” | the nurse saves at 08:00 | one condition record in state Saved is stored for the resident with the time 08:00 and the nurse’s name |
| d1 [yes] (history kept) | the resident already has the 08:00 record “Stable” | the nurse saves “Fever” at 14:00 | the resident has two records; the 08:00 record is unchanged (FR-02-02) |
| d1 [no] | the nurse has selected the resident but left the condition empty | the nurse saves | no condition record is stored and earlier records are unchanged; what the nurse sees is open (#1) |
| d1 [no] → d1 [yes] | a save was rejected because the condition was empty | the nurse enters “Stable” and saves again | exactly one condition record is stored; whether the nurse may do this is open (#2) |

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
| 1 | [NEEDS CLARIFICATION: what makes a condition invalid, what must the system do then, and is the nurse told why? FR-02-01 only describes a successful save.] (nodes `d1`, `s0`) | Yes | Tests use “condition left empty” as the invalid case; nothing is saved. |
| 2 | [NEEDS CLARIFICATION: may the nurse correct the condition and submit it again after a rejected save?] (nodes `a3`, `n2`) | No | The nurse may correct and save again. Nothing is saved in between. |

## 7. Verification log

| Check | Result | By | Date |
| --- | --- | --- | --- |
| Renders on mermaid.live | Pass on a local mermaid-cli render; recheck on mermaid.live |  |  |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass (`d1`: [yes] / [no]) |  |  |
| No action has two incoming flows (merge used instead) | Pass (`m1` receives `a1` and `a3`; `stop1` has one incoming flow) |  |  |
| Every fork has a matching join | N/A (no fork) |  |  |
| Every system action cites an FR | Pass for `s1` and `s2`; the object node `o1` cites FR-02-02. `d1` cites none (see #1). The NEEDS CLARIFICATION nodes `s0` and `n2` are open questions, not actions. |  |  |
| Every FR of this use case appears in the diagram | Pass (2 of 2: FR-02-01, FR-02-02) |  |  |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | Pass (“Nurse” from the Use Case Diagram, “Dien Hong system” from the Context Diagram) |  |  |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check |  |  |
| AI critic prompt run, findings resolved or logged as open questions | Run once in this chat on an earlier version of this diagram; findings logged as open questions #1 and #2. Re-run on this version before review. |  |  |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes the UC-05 diagram needs in the function table.

| FR | Function | Change |
| --- | --- | --- |
| FR-02-01 | Update condition | **Change** the EARS criterion: “WHEN a nurse selects an existing resident, enters the resident’s condition and saves, the system SHALL store it for that resident with the time and the nurse’s name”. This covers `a1`. |
| FR-02-02 | Keep condition history | None to the function table. |
| (data model) | Resident Condition Record state | None to the function table.  A condition recorded in the state `Saved`. This covers `o1`. |
| (no ID yet) | Validate condition | **New function, only if the team answers open question #1:** “IF the condition is invalid, THEN the system SHALL reject the save, leave earlier records unchanged, and tell the nurse why.” This covers `d1` and `s0`. If the team decides nothing can be invalid, no FR is needed and the branch is deleted. |
| (no ID yet) | Correct condition | **New function, only if the team answers open question #2:** “IF the condition is invalid, THEN the system SHALL let the nurse correct the condition and save it again.” This covers `n2` and `a3`. If the answer is no, delete both nodes and the loop through `m1`. |

---

Template source: DBIZ2 Lesson 7, VJCBI College - FTU. Notation: OMG UML 2.5.1, Clause 15.