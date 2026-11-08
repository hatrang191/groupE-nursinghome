# Usage Flow: Dien Hong System

| Field   | Value                                                                                |
| ------- | ------------------------------------------------------------------------------------ |
| Product | Dien Hong System                                                                     |
| Version | v0.1                                                                                 |
| Date    | 2026-11-06                                                                           |
| Sources | W0 Table 1 (use case inventory); Use Case Diagram; `docs/activity/activity-index.md` |
|         |                                                                                      |

## Receptionist: manage resident profile

```mermaid
flowchart LR
  A(["Receptionist needs to register a new resident or keep a resident's information up to date"]) --> D{"Is the resident new?"}
  D -->|"Yes"| U1["UC-01: Create new resident profile"]
  D -->|"No"| U2["UC-02: Update resident information"]
  U1 --> Z(["The resident's profile is correct in the system"])
  U2 --> Z
```

| Step | Use case                           | Activity diagram | Screens |
| ---- | ---------------------------------- | ---------------- | ------- |
| 1    | UC-01 Create new resident profile  | `AD-UC-01.md`    |   	S-F01-01, S-F01-03, S-F01-02     |
| 2    | UC-02 Update resident information  | `AD-UC-02.md` |  S-F01-02, S-F01-03       |

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
| 1    | UC-03 View resident's profile | `not needed`     |  	S-F01-01, S-F01-02       |
| 2    | UC-04 View condition history  | `not needed`     | 	S-F02-01        |
| 3    | UC-05 Update condition        | `AD-UC-05.md`    |  	S-F02-02       |
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
| 1    | UC-03 View resident's profile                     | `not needed`     |  	S-F01-01, S-F01-02       |
| 2    | UC-04 View condition history                      | `not needed`     |  	S-F02-01       |
| 3    | UC-09 Exchange messages between family and nurses | `AD-UC-09.md`    |  S-F04-01       |

## Residents' Family: stay informed about a resident

```mermaid
flowchart LR
  A(["Family wants news about a resident, or has a question or concern"]) --> U8["UC-08: View resident's condition"]
  U8 --> D{"Does the family want to talk to the nurses?"}
  D -->|"Yes"| U9["UC-09: Exchange messages between family and nurses"]
  U9 --> Z(["The family knows how the resident is and any question is with the nurses"])
  D -->|"No"| Z
```

| Step | Use case                                           | Activity diagram | Screens                    |
| ---- | -------------------------------------------------- | ---------------- | -------------------------- |
| 1    | UC-08 View resident's condition                    | `AD-UC-08.md`    | S-F04-02 |
| 2    | UC-09 Exchange messages between family and nurses  | `AD-UC-09.md`    | 	S-F04-03 |


## BOD: see how the nursing home is running

```mermaid
flowchart LR
  A(["BOD wants to know how the nursing home is running"]) --> U1["UC-10: View reports"]
  U1 --> Z(["BOD has the information needed to decide"])
```

| Step | Use case           | Activity diagram | Screens |
| ---- | ------------------ | ---------------- | ------- |
| 1    | UC-10 View reports | `not needed`     |  S-F05-01       |

## Admin: manage account access

```mermaid
flowchart LR
  A(["Admin needs to give, change or withdraw someone's access, or suspects misuse"]) --> D1{"What is the situation?"}
  D1 -->|"A new user, or a user changes position"| U1["UC-11: Set up accounts"]
  D1 -->|"Misuse is suspected"| U3["UC-14: View access and action logs"]
  U1 --> D2{"Does the user need a new role?"}
  D2 -->|"Yes"| U2["UC-12: Assign role-based permissions"]
  D2 -->|"No"| Z(["Each person's access matches what they are allowed to do"])
  U3 --> U2
  U2 --> Z
```

| Step | Use case                            | Activity diagram | Screens (fill in after Block 2) |
| ---- | ----------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-11 Set up accounts               | `AD-UC-11.md`    |   S-F06-01, S-F06-02                              |
| 2    | UC-14 View access and action logs   | `not needed`     |   	S-F06-05                              |
| 3    | UC-12 Assign role-based permissions | `AD-UC-12.md`    |   S-F06-01, S-F06-03                              |

## Admin: keep the radar working

```mermaid
flowchart LR
  A(["Admin wants to check that the radar is working"]) --> U1["UC-13: Monitor radar status"]
  U1 --> U2["UC-14: View access and action logs"]
  U2 --> Z(["The radar status is known and any problem is traced to its cause"])
```

| Step | Use case                          | Activity diagram | Screens (fill in after Block 2) |
| ---- | --------------------------------- | ---------------- | ------------------------------- |
| 1    | UC-13 Monitor radar status        | `AD-UC-13.md`    |     	S-F06-04                            |
| 2    | UC-14 View access and action logs | `not needed`     |       	S-F06-05                          |


## Use cases without a flow

| Use case | Started by | Described in  |
| -------- | ---------- | ------------- |
| UC-06 Send and acknowledge alert   | Radar, when it detects an abnormal event      | `AD-UC-06.md` |
| UC-07 Receive alert notice   | Radar, through a dangerous alert of UC-06     | `AD-UC-07.md` |

S-F00-01 Sign in is shared by every flow above: each actor passes through it before the first step.

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

