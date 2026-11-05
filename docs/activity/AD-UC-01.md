# Activity Diagram: AD-UC-01 Create new resident profile

| Field | Value |
| :---- | :---- |
| AD ID | AD-UC-01 |
| Use case | UC-01 Create new resident profile |
| Feature | F-01 Resident Profile Management |
| Primary actor | Receptionist |
| Lanes | Receptionist · Dien Hong system |
| Inputs (L3-L5) | Context Diagram (Receptionist) · Use Case Diagram (UC-01) · Sequence: SD-UC-01 · W1 rows FR-01-01-FE, FR-01-01-BE |
| Trigger | The receptionist starts a new resident profile |
| Preconditions | The receptionist is signed in and their role allows creating residents (FR-06-03). |
| Success end state | One `Resident Profile` with a unique resident ID is saved, and the receptionist sees it. |
| Failure end state(s) | No profile is saved while the details are invalid; the receptionist sees the validation errors and can correct them. |
| FRs covered | FR-01-01 |
| Business rules applied | None (W1 has no BR rows) |
| Version / status | v0.1 · Draft |
| Author | Lê Hà Thanh |

1\. Diagram

Notation follows the DBIZ2 activity profile (`mermaid-activity-patterns.md`). Rendered with `swimlane-beta`, which needs Mermaid 11.16 or later.

```mermaid
---
config:
  flowchart:
    rankSpacing: 80
    nodeSpacing: 80
---
swimlane-beta TB
  subgraph REC["Receptionist"]
    start@{ shape: sm-circ }
    merge{" "}
    enter("Enter or correct the resident's details")
    submit("Submit the details")
    view("See the profile created with its resident ID")
    finish@{ shape: fr-circ }
  end
  subgraph SYS["Dien Hong system"]
    validate("Validate the details (FR-01-01)")
    valid{"Information valid?"}
    errors("Display the validation errors (FR-01-01)")
    generate("Generate a unique resident ID (FR-01-01)")
    save("Save the resident profile (FR-01-01)")
    profile["Resident Profile"]
  end
  start --> merge --> enter --> submit --> validate --> valid
  valid -->|"[valid information]"| generate --> save --> profile --> view --> finish
  valid -->|"[invalid information]"| errors
  errors --> merge
```

&nbsp;

Reading notes:

- `merge` is a **merge** node. After the errors are shown, the flow returns through it, never straight into an action: an action with two incoming control flows would wait for both (OMG UML 2.5.1, sub clause 15.2.3.2).  
- The guards copy the `alt` fragment of SD-UC-01: `[valid information]` / `[invalid information]`.  
- `Resident Profile` is drawn without a state: no use case in Release 1 changes a resident's lifecycle (no discharge, no delete), so there is no status value to show.

## 2\. Node trace

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| validate | Action | Validate the details | FR-01-01 |
| valid | Decision | Information valid? | FR-01-01 (IF clause) |
| errors | Action | Display the validation errors | FR-01-01 |
| generate | Action | Generate a unique resident ID | FR-01-01 |
| save | Action | Save the resident profile | FR-01-01 |
| profile | Object node | Resident Profile | FR-01-01 |

## 3\. Branches and acceptance tests

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| valid \[invalid information\] | the receptionist has filled in a new resident's details but left the full name empty | they submit the details | no Resident Profile is saved and the validation errors are shown |
| valid \[valid information\] | all required details of resident "Nguyễn Văn An" are filled in correctly | the receptionist submits the details | one Resident Profile is saved with a resident ID that no other profile has, and the receptionist sees the profile with that ID |
| valid \[invalid information\] → valid \[valid information\] | a submission was rejected because the full name was empty | the receptionist fills in the full name and submits again | exactly one Resident Profile is saved |

## 4\. Object states used

| Object | States in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Resident Profile | none | none defined in Release 1 |

## 5\. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
| :---- | :---- |
|  |  |

## 6\. Open questions

| \# | Question | Blocking? | Default applied |
| :---- | :---- | :---- | :---- |
| 1 | \[NEEDS CLARIFICATION: which fields are required, and what makes a detail invalid? FR-01-01 does not list the rules.\] | Yes | Tests use "full name left empty" as the invalid case until the client gives the field list. |
| 2 | \[NEEDS CLARIFICATION: where is the resident's room (and so their radar) linked to the profile? Receive Alert needs it.\] | No | Not part of UC-01. |
| 3 | \[NEEDS CLARIFICATION: who links family accounts to the resident? FR-03-03 and FR-04-01 need the link.\] | No | Done in Set up accounts (UC-10), not here. |
| 4 | \[NEEDS CLARIFICATION: there is no sign-in use case, so the precondition "signed in" points at nothing.\] | No | Kept as a precondition; replace with a call step once a sign-in use case exists. |

## 7\. Verification log

| Check | Result | By | Date |
| :---- | :---- | :---- | :---- |
| Renders on mermaid.live | Pass on a local Mermaid 12.2 render; recheck on mermaid.live |  |  |
| Every decision has guards on all outgoing flows, no overlap, no gap | Pass |  |  |
| No action has two incoming flows (merge used instead) | Pass |  |  |
| Every fork has a matching join | N/A (no fork) |  |  |
| Every system action cites an FR | Pass |  |  |
| Every FR of this use case appears in the diagram | Pass (1 of 1\) |  |  |
| Lane names match the Context Diagram and the use case actors; no Web App, API or database lanes | Pass |  |  |
| Related sequence diagram uses the same names and FR IDs; its alt fragments appear here as decisions | Pass (SD-UC-01 alt → decision `valid`) |  |  |
| AI critic prompt run, findings resolved or logged as open questions | Not run yet |  |  |

---

Template source: DBIZ2 Lesson 7, VJCBI College \- FTU.&nbsp;

Notation: OMG UML 2.5.1, Clause 15\.
