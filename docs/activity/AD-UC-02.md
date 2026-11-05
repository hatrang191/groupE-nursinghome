# Activity Diagram: AD-UC-02 Update resident information

| Field | Value |
| :---- | :---- |
| **AD ID** | AD-UC-02 |
| **Use case** | UC-02 Update resident information |
| **Feature** | F-01 Resident Profile Management |
| **Primary actor** | Receptionist |
| **Lanes** | Receptionist · Dien Hong system |
| **Inputs (L3-L5)** | Context Diagram (Receptionist) · Use Case Diagram (UC-02) · Sequence: SD-UC-02 · W1 rows FR-01-02-FE, FR-01-02-BE |
| **Trigger** | The receptionist selects a resident whose information needs to be updated. |
| **Preconditions** | The receptionist is signed in and has permission to update resident information. The resident profile already exists. |
| **Success end state** | The resident profile is updated and the receptionist sees the updated information. The system stores the changes with the time and staff name. |
| **Failure end state(s)** | If the changes are invalid, the system displays validation errors and no successful update is completed; the receptionist can return to editing the information. |
| **FRs covered** | FR-01-02 |
| **Business rules applied** | No business-rule IDs are identified in the supplied UC-02 diagram. |
| **Version / status** | v0.1 · Draft |
| **Author** | Trúc Anh |

1\. Diagram

Notation follows the DBIZ2 activity profile (`mermaid-activity-patterns.md`). The diagram uses `swimlane-beta`, which requires Mermaid 11.16 or later.

```mermaid
---
config:
  flowchart:
    rankSpacing: 80
    nodeSpacing: 80
---
swimlane-beta LR
  subgraph REC["Receptionist"]
    direction TB
    start@{ shape: sm-circ }
    a1("Select the resident")
    m1{" "}
    a2("Edit or correct the resident's information")
    a3("Save the changes")
    a4("See the updated information")
    stop@{ shape: fr-circ }

    start --> a1
    m1 --> a2
    a2 --> a3
    a4 --> stop
  end

  subgraph SYS["Dien Hong system"]
    direction TB
    s1("Show the resident's current information (FR-01-02)")
    s2("Validate the changes (FR-01-02)")
    d1{"Information valid?"}
    s3("Display the validation errors (FR-01-02)")
    s4("Store the changes with the time and the staff name (FR-01-02)")
    o1["Resident Profile"]
  end

  a1 --> s1
  s1 --> m1
  a3 --> s2
  s2 --> d1
  d1 -->|"[invalid information]"| s3
  s3 --> m1
  d1 -->|"[valid information]"| s4
  s4 --> o1
  o1 --> a4
```

&nbsp;

### Reading notes

- `m1` is the merge node in the **Receptionist** lane. It brings the initial path from showing the current resident information and the correction path after validation errors back to the editing action.  
- The decision guards are copied exactly from the supplied UC-02 diagram: `[valid information]` and `[invalid information]`.  
- `Resident Profile` is shown as an object node. The supplied diagram does not specify an object state.  
- The system actions are labelled with `FR-01-02`, matching the supplied diagram.

## 2\. Node trace

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| `a1` | Action | Select the resident | UC-02 flow; no FR label shown on this actor action |
| `s1` | Action | Show the resident's current information | FR-01-02 |
| `m1` | Merge | Merge the initial path and the correction path | Control-flow node |
| `a2` | Action | Edit or correct the resident's information | UC-02 flow; no FR label shown on this actor action |
| `a3` | Action | Save the changes | UC-02 flow; no FR label shown on this actor action |
| `s2` | Action | Validate the changes | FR-01-02 |
| `d1` | Decision | Information valid? | FR-01-02 (decision / validation outcome) |
| `s3` | Action | Display the validation errors | FR-01-02 |
| `s4` | Action | Store the changes with the time and the staff name | FR-01-02 |
| `o1` | Object node | Resident Profile | FR-01-02 |
| `a4` | Action | See the updated information | UC-02 flow; no FR label shown on this actor action |

## 3\. Branches and acceptance tests

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| `d1` `[invalid information]` | The receptionist has edited a resident's information, but at least one change does not pass validation. | The receptionist saves the changes. | The system displays validation errors; the receptionist can return to the editing action. |
| `d1` `[valid information]` | The receptionist has edited the resident's information and the changes pass validation. | The receptionist saves the changes. | The system stores the changes with the time and staff name, and the receptionist sees the updated information. |
| `[invalid information]` → `[valid information]` | The first submission was rejected and validation errors were displayed. | The receptionist corrects the information and saves again. | After the changes pass validation, the system stores the changes and the receptionist sees the updated information. |

**Note:** These acceptance tests are derived from the branches and actions in the supplied diagram. The exact field-level validation rules are not specified there.

## 4\. Object states used

| Object | States in this diagram | Full state list (all diagrams) |
| :---- | :---- | :---- |
| Resident Profile | None shown | Not specified in the supplied UC-02 diagram |

## 5\. Screens touched

Filled in during Lesson 8, from the Screen List.

| Actor action | Screen ID |
| :---- | :---- |
|  |  |
|  |  |

## 6\. Open questions

| \# | Question | Blocking? | Default applied |
| :---- | :---- | :---- | :---- |
| 1 | **\[NEEDS CLARIFICATION: which fields can be updated, which fields are required, and what makes an update invalid?\]** The supplied diagram does not enumerate the validation rules. | Yes, for detailed validation acceptance tests | Tests describe only a generic invalid change until the field-level rules are confirmed. |
| 2 | **\[NEEDS CLARIFICATION: what happens if the resident profile changes between selection and save?\]** The supplied diagram does not describe concurrent updates. | No, unless concurrent editing is in scope | No concurrency behaviour is added to the diagram. |
| 3 | **\[NEEDS CLARIFICATION: which screen IDs correspond to selecting, editing, saving, and viewing the updated resident profile?\]** | No | Screen IDs are left blank pending the Screen List. |
| 4 | **\[NEEDS CLARIFICATION: does FR-01-02 define the audit fields and timestamp format?\]** The diagram states that the system stores the time and staff name but does not specify the format or audit-retention rule. | No | The action text is retained exactly as supplied; no extra audit rules are assumed. |

## 7\. Verification log

| Check | Result | By | Date |
| :---- | :---- | :---- | :---- |
| Diagram uses `swimlane-beta` and the two supplied lanes | Pass (source-level review) |  |  |
| Decision has guards on all outgoing flows | Pass: `[invalid information]` and `[valid information]` |  |  |
| Merge node is in the Receptionist lane | Pass (matches the supplied UC-02 diagram) |  |  |
| System actions cite an FR | Pass: system actions use `FR-01-02` |  |  |
| All requirements for UC-02 are represented | Not independently verified; only the supplied diagram was available as the UC-02 source |  |  |
| Diagram renders on Mermaid 11.16 or later | Not run in this document |  |  |
| Screen IDs confirmed against Screen List | Pending |  |  |
| Validation rules confirmed against FR-01-02 | Pending |  |  |

&nbsp;

**Template source:**  DBIZ2 Lesson 7, VJCBI College \- FTU.  
**Notation reference:** `mermaid-activity-patterns.md` (`swimlane-beta`).