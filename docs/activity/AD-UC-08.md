# Activity Diagram: AD-UC-08 View resident’s condition

| Field | Value |
| --- | --- |
| AD ID | `AD-UC-08` |
| Use case | `UC-08 View resident's condition` |
| Feature | `F-04 Family Communication` |
| Primary actor | Residents’ Family |
| Lanes | Residents’ Family · Dien Hong system |
| Inputs (L3-L5) | Context Diagram (Residents’ Family ↔︎ Dien Hong system: Current status, status history, incident alerts) · Use Case Diagram (View resident’s condition, in the Family mobile app) · W1 rows FR-04-01 FE “Verify family access to resident” FR-04-02-FE, FR-04-02-BE (View resident’s condition) |
| Trigger | A family member opens the mobile app to see the condition of one of their residents (FR-04-02) |
| Preconditions | The family member is signed in and their account is linked to one or more residents when the account is created (UC-11). A condition may or may not have been recorded for a resident (UC-05). |
| Success end state | The family member sees the current condition of the resident they selected from their linked residents, or is told that no condition has been recorded yet for that resident. Nothing is changed. |
| Failure end state(s) | Access to the selected resident cannot be verified: no condition is shown and the family member sees an access-denied message (FR-04-01). |
| FRs covered | `FR-04-01`, `FR-04-02` |
| Business rules applied | None (W1 has no BR rows) |
| Version / status | v0.1 · Draft |
| Author | Nguyen Tran Khanh Linh |

## 1. Diagram

Notation follows the DBIZ2 activity profile (`mermaid-activity-patterns.md`). Rendered with `swimlane-beta`, which needs Mermaid 11.16 or later.

```mermaid
swimlane-beta TB
  subgraph FAM["Residents' Family"]
    start@{ shape: sm-circ }
    a1("Select one of own linked residents")
    a2("Read the current condition")
    a3("Read the access-denied message")
    a4("Read that no condition has been recorded yet")
    stop1@{ shape: fr-circ }
  end
  subgraph SYS["Dien Hong system"]
    s0("Verify the family member's access to the selected resident (FR-04-01)")
    d1{"Access verified?"}
    s2("Deny the request with an access-denied message (FR-04-01)")
    d2{"Condition recorded?"}
    s3("Tell the family member that no condition has been recorded yet (FR-04-02)")
    o1["Resident Condition Record [Saved]"]
    s1("Display the current condition of the selected resident (FR-04-02)")
  end
  start --> a1 --> s0 --> d1
  d1 -->|"[access not verified]"| s2 --> a3 --> stop1
  d1 -->|"[access verified]"| d2
  d2 -->|"[condition recorded]"| o1 --> s1 --> a2 --> stop1
  d2 -->|"[none recorded]"| s3 --> a4 --> stop1
```

Reading notes:

- No flow returns to an earlier node, so the diagram needs no merge node. Three flows end at `stop1` (`a2`, `a3`, `a4`); this is allowed because a final node is not an action.
- The access is checked by FR-04-01 “Verify family access to resident”. `s0`, `d1` and `s2` trace to it, and the guards copy its IF clause.
- The no-condition case stays inside FR-04-02 as an IF clause. `d2` and `s3` trace to it, and `a4` is the family member’s side of `s3`.
- `o1` is read, not written. UC-05 writes the record in state `Saved`; this use case only reads it. “Current condition” is taken as the most recent record, because FR-02-03 keeps every earlier one.
- One family account can link to several residents, so `a1` has the family member choose one. The link itself is created in Set up accounts and is not drawn here.

## 2. Node trace

| Node | Kind | Text | Traces to |
| --- | --- | --- | --- |
| s0 | Action | Verify the family member’s access to the selected resident | FR-04-01 |
| d1 | Decision | Access verified? | FR-04-01 (IF clause) |
| s2 | Action | Deny the request with an access-denied message | FR-04-01 |
| d2 | Decision | Condition recorded? | FR-04-02 (IF clause) |
| s3 | Action | Tell the family member that no condition has been recorded yet | FR-04-02 |
| o1 | Object node | Resident Condition Record [Saved] | FR-04-02; state `Saved` agreed in AD-UC-05 |
| s1 | Action | Display the current condition of the selected resident | FR-04-02 |

## 3. Branches and acceptance tests

The names and conditions below are examples. W1 does not list the allowed conditions.

| Branch | Given | When | Then |
| --- | --- | --- | --- |
| d1 [access verified], d2 [condition recorded] | the account of family member “Nguyễn Thị Lan” is linked to residents “Nguyễn Văn An” and “Lê Thị Hoa”; An’s latest record at 14:00 says “Fever” | the family member selects “Nguyễn Văn An” | the family member sees “Fever” for Nguyễn Văn An and nothing about Lê Thị Hoa |
| d1 [access verified] (several residents) | the same account, and Hoa’s latest record says “Stable” | the family member selects “Lê Thị Hoa” | the family member sees “Stable” for Lê Thị Hoa only |
| d2 [condition recorded] (latest shown) | the resident has the 08:00 record “Stable” and the 14:00 record “Fever” | the linked family member selects the resident | the family member sees “Fever”, the most recent record |
| d1 [access not verified] | the account of family member “Nguyễn Thị Lan” is not linked to resident “Phạm Văn Bình” (or to any resident) | the family member asks to see his condition | no condition is shown and an access-denied message is shown |
| d2 [none recorded] | resident “Nguyễn Văn An” was just created and no condition has been saved | the linked family member selects him | the family member is told that no condition has been recorded yet and no other condition is shown |

## 4. Object states used

| Object | States in this diagram | Full state list (all diagrams) |
| --- | --- | --- |
| Resident Condition Record | Saved (read) | Saved |

## 5. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
| --- | --- |
|  |  |

## 6. Open questions

| # | Question | Blocking? | Default applied |
| --- | --- | --- | --- |
| 1 | [NEEDS CLARIFICATION: how can the system know if the family access is valid or not?] (nodes `s0`, `d1`) | No | The system verifies access by checking that the selected resident is one of the residents linked to that family account (the link created in UC-11). If it is not linked, access is denied. |
| 2 | [NEEDS CLARIFICATION: what must the family see when no condition has been recorded for the selected resident yet? FR-04-02 only describes a resident with a condition.] (nodes `d2`, `n1`) | No | The family member is told that no condition has been recorded yet; nothing else is shown. |

## 7. Verification log

| Check | Result | By | Date |
| --- | --- | --- | --- |
| Renders on mermaid.live | Pass on a local mermaid-cli render; recheck on mermaid.live |  |  |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass (`d1`: [access verified] / [access not verified]; `d2`: [condition recorded] / [none recorded]) |  |  |
| No action has two incoming flows (merge used instead) | Pass (only `stop1` has several incoming flows, and it is a final node) |  |  |
| Every fork has a matching join | N/A (no fork) |  |  |
| Every system action cites an FR | Pass for `s0`, `s1`, `s2` and `s3`. Both decisions cite an FR. |  |  |
| Every FR of this use case appears in the diagram | Pass (2 of 2: FR-04-01, FR-04-02) |  |  |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | Pass (“Residents’ Family” from the Context Diagram and Use Case Diagram, “Dien Hong system” from the Context Diagram) |  |  |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | To check |  |  |
| AI critic prompt run, findings resolved or logged as open questions | Not run yet |  |  |

## Notes for the integrator: changes needed in W1

An action that cannot be traced is a missing FR. These are the changes the UC-08 diagram needs in the function table.

| FR | Function | Change |
| --- | --- | --- |
| FR-04-01 (proposed ID) | Verify family access to resident | **New function answering the open question #1** (F-04, same priority as View resident’s condition; FP type NC, a rule inside another transaction): “IF the family member’s access to the selected resident cannot be verified, THEN the system SHALL deny the request with an access-denied message.” This covers `s0`, `d1`, `s2` and `a3`.  |
| FR-04-02 (FR-04-01 previously) | View resident’s condition | Adjusting the old function to  answer open question #2. One family account can link to several residents, so name the selected resident: “WHEN a family member selects one of their linked residents, the system SHALL display the current condition of that resident. IF no condition has been recorded for that resident, THEN the system SHALL tell the family member that no condition has been recorded yet.” This covers `a1`, `d2`, `n1`, `a4`, `s1` and `o1.` |

---

Template source: DBIZ2 Lesson 7, VJCBI College - FTU. Notation: OMG UML 2.5.1, Clause 15.
