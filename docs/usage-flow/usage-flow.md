# Usage Flow: Dien Hong System

| Field   | Value                                                                                |
| ------- | ------------------------------------------------------------------------------------ |
| Product | Dien Hong System                                                                     |
| Version | v0.1                                                                                 |
| Date    | 2026-11-06                                                                           |
| Sources | W0 Table 1 (use case inventory); Use Case Diagram; `docs/activity/activity-index.md` |
|         |                                                                                      |

## Receptionist: create a new resident profile

  

``` mermaid

flowchart LR

  A(["Receptionist needs to register a new resident"]) --> U1["UC-01: Create new resident profile"]

  U1 --> Z(["New resident profile is created"])

```

| Step | Use case                          | Activity diagram | Screens |
| ---- | --------------------------------- | ---------------- | ------- |
| 1    | UC-01 Create new resident profile | ``AD-UC-01.md``  |         |

## Receptionist: update resident information

  

``` mermaid

flowchart LR

  A(["Receptionist needs to update an existing resident's information"]) --> U1["UC-02: Update resident information"]

  U1 --> Z(["Resident information is updated"])

```

| Step | Use case                          | Activity diagram   | Screens |
| ---- | --------------------------------- | ------------------ | ------- |
| 1    | UC-02 Update resident information | ``AD-UC-02(1).md`` |         |
## Nurse: keep a resident's condition up to date

  

```mermaid

flowchart LR

 A(["Nurse checks on a resident"]) --> U3["UC-03 View resident's profile"]

 U3 --> U4["UC-04 View condition history"]

 U4 --> U5["UC-05 Update condition"]

 U5 --> Z(["New condition saved in the history"])

```


| Step | Use case                      | Activity diagram | Screens |
| ---- | ----------------------------- | ---------------- | ------- |
| 1    | UC-03 View resident's profile | `not needed`     |         |
| 2    | UC-04 View condition history  | `not needed`     |         |
| 3    | UC-05 Update condition        | `AD-UC-05.md`    |         |
## Nurse: keep a resident's family informed

  

```mermaid

flowchart LR

 A(["Family needs news about a resident"]) --> U3["UC-03 View resident's profile"]

 U3 --> U4["UC-04 View condition history"]

 U4 --> U9["UC-09 Exchange messages between family and nurses"]

 U9 --> Z(["Family informed"])

```


| Step | Use case                                          | Activity diagram | Screens |
| ---- | ------------------------------------------------- | ---------------- | ------- |
| 1    | UC-03 View resident's profile                     | `not needed`     |         |
| 2    | UC-04 View condition history                      | `not needed`     |         |
| 3    | UC-09 Exchange messages between family and nurses | `AD-UC-09.md`    |         |

## Residents' Family: know how a resident is

```mermaid
flowchart LR
 A(["Family wants news about a resident, or got an alert notice"]) --> U8["UC-08 View resident's condition"]
 U8 --> Z(["Family knows the current condition of the resident"])
```

|   |   |   |   |
|---|---|---|---|
|Step|Use case|Activity diagram|Screens|
|1|UC-08 View resident's condition|AD-UC-08.md|TBD: no screen-list.md yet|

## Residents' Family: talk to the nurses about a resident

```mermaid
flowchart LR
 A(["Family has a question or concern about a resident"]) --> U8["UC-08 View resident's condition"]
 U8 --> U9["UC-09 Exchange messages between family and nurses"]
 U9 --> Z(["Message is in the conversation with the nurses"])
```

|   |   |   |   |
|---|---|---|---|
|Step|Use case|Activity diagram|Screens|
|1|UC-08 View resident's condition|AD-UC-08.md|TBD: no screen-list.md yet|
|2|UC-09 Exchange messages between family and nurses|AD-UC-09.md|TBD: no screen-list.md yet|



**

## BOD: see how the nursing home is running

```mermaid
flowchart LR
  A(["BOD wants to know how the nursing home is running"]) --> U1["UC-10: View reports"]
  U1 --> Z(["BOD has the information needed to decide"])
```

| Step | Use case           | Activity diagram | Screens |
| ---- | ------------------ | ---------------- | ------- |
| 1    | UC-10 View reports | `not needed`     |         |

## Admin: give a new user access

```mermaid
flowchart LR
  A(["A new staff member or family member needs to use the system"]) --> U1["UC-11: Set up accounts"]
  U1 --> U2["UC-12: Assign role-based permissions"]
  U2 --> Z(["The new user has an account with the right role and permissions"])
```

| Step | Use case                            | Activity diagram | Screens (fill in after Block 2) |
| ---- | ----------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-11 Set up accounts               | `AD-UC-11.md`    |                                 |
| 2    | UC-12 Assign role-based permissions | `AD-UC-12.md`    |                                 |

## Admin: correct permissions after irregular activity

```mermaid
flowchart LR
  A(["Admin suspects that someone uses the system in a way they should not"]) --> U1["UC-14: View access and action logs"]
  U1 --> U2["UC-12: Assign role-based permissions"]
  U2 --> Z(["Permissions match what each person is allowed to do"])
```

| Step | Use case                            | Activity diagram | Screens (fill in after Block 2) |
| ---- | ----------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-14 View access and action logs   | `not needed`     |                                 |
| 2    | UC-12 Assign role-based permissions | `AD-UC-12.md`    |                                 |

## Admin: keep the radar working

```mermaid
flowchart LR
  A(["Admin wants to check that the radar is working"]) --> U1["UC-13: Monitor radar status"]
  U1 --> U2["UC-14: View access and action logs"]
  U2 --> Z(["The radar status is known and any problem is traced to its cause"])
```

| Step | Use case                          | Activity diagram | Screens (fill in after Block 2) |
| ---- | --------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-13 Monitor radar status        | `AD-UC-13.md`    |                                 |
| 2    | UC-14 View access and action logs | `not needed`     |                                 |

## Admin: withdraw or change access when staff leave or change position

```mermaid
flowchart LR
  A(["A staff member leaves or changes position"]) --> U1["UC-11: Set up accounts"]
  U1 --> D{"Does the person keep working in a new position?"}
  D -->|"Yes"| U2["UC-12: Assign role-based permissions"]
  D -->|"No"| U3["UC-14: View access and action logs"]
  U2 --> U3
  U3 --> Z(["Access matches the person's current position and has been checked"])
```

| Step | Use case                                                                                 | Activity diagram | Screens (fill in after Block 2) |
| ---- | ---------------------------------------------------------------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-11 Set up accounts (edit the account, or deactivate it when the person leaves)        | `AD-UC-11.md`    |                                 |
| 2    | UC-12 Assign role-based permissions (only if the person keeps working in a new position) | `AD-UC-12.md`    |                                 |
| 3    | UC-14 View access and action logs                                                        | `not needed`     |                                 |


## Use cases without a flow

| Use case | Started by | Described in  |
| -------- | ---------- | ------------- |
| UC-06    | Radar      | `AD-UC-06.md` |
| UC-07    | Radar      | `AD-UC-07.md` |

## Checks before you save

- [x] Every actor that starts a use case has at least one flow.
- [x] An actor with clearly different goals has one flow per goal; no flow has more than about
      9 boxes.
- [x] Every actor name matches the Use Case Diagram and the Context Diagram.
- [x] Coverage: every in-scope use case of W0 Table 1 (Must, Should, Could) that a person starts
      appears in at least one flow, with the same ID and name. A use case may appear in several
      flows. Use cases started by time or an external system are listed under "Use cases without
      a flow". Won't-have use cases appear nowhere.
- [x] No box is a click or a screen; no box tells two use cases.
- [x] Every flow renders on mermaid.live.
- [ ] After Block 2: every step has at least one screen, and every screen in the screen list
      appears in some step.

