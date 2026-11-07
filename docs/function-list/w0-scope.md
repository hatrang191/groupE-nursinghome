# W0 Scope: Dien Hong System

Lesson 5 (Function List workshop, block W0). Inputs: [CVCA](../context/cvca.md), [Context Diagram](../context/context-diagram.md), [Use Case Diagram](../use-case/use-case-diagram.md).

## Table 1. Use case inventory

| Use case ID | Use case name                               | Primary actor            | Assigned feature | Login required | Priority |
|-------------|---------------------------------------------|--------------------------|------------------|----------------|----------|
| UC-01       | Create new resident profile                 | Receptionist             | F-01             | Yes            | Must     |
| UC-02       | Update resident information                 | Receptionist             | F-01             | Yes            | Should   |
| UC-03       | View resident's profile                     | Nurse                    | F-01             | Yes            | Must     |
| UC-04       | View condition history                      | Nurse                    | F-02             | Yes            | Should   |
| UC-05       | Update condition                            | Nurse                    | F-02             | Yes            | Must     |
| UC-06       | Send and acknowledge alert                  | Radar                    | F-03             | No             | Must     |
| UC-07       | Receive alert notice                        | Residents' Family        | F-03             | Yes            | Must     |
| UC-08       | View resident's condition                   | Residents' Family        | F-04             | Yes            | Should     |
| UC-09       | Exchange messages between family and nurses | Nurse, Residents' Family | F-04             | Yes            | Should     |
| UC-10       | View reports                                | BOD                      | F-05             | Yes            | Could    |
| UC-11       | Set up accounts                             | Admin                    | F-06             | Yes            | Must     |
| UC-12       | Assign role-based permissions               | Admin                    | F-06             | Yes            | Must     |
| UC-13       | Monitor radar status                        | Admin                    | F-06             | Yes            | Could   |
| UC-14       | View access and action logs                 | Admin                    | F-06             | Yes            | Could    |

UC-07 was added after the Lesson 7 Inputs check (see [activity-index](../activity/activity-index.md)).

## Table 2. Feature list (flows into W1)

| Feature ID | Feature name (business noun) | Scope in one sentence | Primary actors served | Owner | Use cases covered |
|------------|------------------------------|-----------------------|-----------------------|-------|-------------------|
| F-00 | Platform and Access | Every user signs in with their own account and reaches only the screens of their role. | Receptionist, Nurse, BOD, Admin, Residents’ Family | Nguyễn Trần Khánh Linh | none (platform function) |
| F-01 | Resident Profile Management | Staff create, update and view resident profiles so every resident has accurate, up-to-date basic information. | Receptionist, Nurse | Nguyễn Thị Trúc Anh | UC-01, UC-02, UC-03 |
| F-02 | Condition Tracking | Nurses record each resident's condition and review its history over time. | Nurse | Nguyễn Hà Linh Trang | UC-04, UC-05 |
| F-03 | Abnormal Alert Handling | The radar system detects abnormal events and sends alerts that nurses and families receive immediately as push notifications. | Radar, Nurse, Residents' Family | Lê Hà Thanh | UC-06, UC-07 |
| F-04 | Family Communication | Families follow their relative's condition and chat directly with nurses through the web app on their phone. | Residents' Family, Nurse | Nguyễn Trần Khánh Linh | UC-08, UC-09 |
| F-05 | Dashboard & Reporting | The Board of Directors views summary reports on care and operations. | BOD | Trần Lê Bảo An | UC-10 |
| F-06 | System Administration | Admins set up accounts, assign role-based permissions, monitor radar devices and audit access and action logs. | Admin | Lê Thị Thùy Linh | UC-11, UC-12, UC-13, UC-14 |

Feature count: 7 (allowed 3 to 6): REVIEW NEEDED. Coverage against the CVCA: all primary actors (Receptionist, Nurse, Residents' Family, BOD, Admin) are served.

## Table 3. Won't-have (recorded, never dropped silently)

| Excluded from scope  | How it surfaced                                                      | Reason for exclusion       |
|----------------------|----------------------------------------------------------------------|----------------------------|
| Family Visit Booking | Proposed by group members as a function to improve family experience | Out of scope for Release 1 |
| External Services    | Proposed by group members as a function                              | Out of scope for Release 1 |
| QR Payment           | Proposed by group members as a function                              | Out of scope for Release 1 |

## Table 4. Data functions

| Data group                     | ILF / EIF | Why                                                                | Function points |
|--------------------------------|-----------|--------------------------------------------------------------------|-----------------|
| Resident Profile               | ILF       | App creates and keeps every resident's basic information           | 10              |
| Resident Condition             | ILF       | App creates and keeps every condition record and its history        | 10              |
| Alert Log                      | ILF       | App stores each abnormal event alert and who received it            | 10              |
| Chat Message                   | ILF       | App creates and keeps messages between families and nurses          | 10              |
| User Account & Role            | ILF       | App keeps user accounts and their role-based permissions            | 10              |
| Access & Action Log            | ILF       | App records every sign-in and user action                           | 10              |
| Radar Sensor                   | EIF       | Abnormal events and device status are read from the radar system    | 7               |
| **Total data-function points** |           |                                                                    | **67**          |

The data group names are the names Screen Specs use as data sources (Lesson 8).

## Business rules

The rules below consolidate business constraints currently stated or assumed in W0/W1, the activity diagrams, and architecture documents. Items marked **Needs confirmation** are provisional defaults, not approved client rules. Confirm them with the client and update the status and linked requirements when resolved.

| BR ID | Business rule | Applies to | Source / trace | Status |
|-------|---------------|------------|----------------|--------|
| BR-01-01 | Each saved Resident Profile shall have a unique resident ID. | UC-01 | FR-01-01; [AD-UC-01](../activity/AD-UC-01.md) | Stated in the use-case activity; confirm against the approved requirement. |
| BR-01-02 | A resident profile shall be saved or updated only when its required information is valid. The required fields and validation criteria must be defined before implementation. | UC-01, UC-02 | FR-01-01, FR-01-02; open questions in [AD-UC-01](../activity/AD-UC-01.md) and [AD-UC-02](../activity/AD-UC-02.md) | Needs confirmation: validation criteria are not specified. |
| BR-02-01 | Each valid condition update shall be saved as a new record; existing condition records shall not be overwritten. | UC-05 | FR-02-03; [AD-UC-05](../activity/AD-UC-05.md) | Stated in the activity diagram. |
| BR-02-02 | The current condition shown to a family member shall be the resident's most recently saved condition record. | UC-08 | [AD-UC-08](../activity/AD-UC-08.md), reading note and acceptance tests | Needs confirmation: documented as the interpretation of “current condition.” |
| BR-03-01 | An alert is dangerous when its severity is High. | UC-07 | FR-03-09; [AD-UC-07](../activity/AD-UC-07.md) | Needs client confirmation; currently an assumption. |
| BR-03-02 | Send one first notice for a dangerous alert, and do not send a first notice for a non-dangerous alert. | UC-07 | FR-03-09; SyR-06; [AD-UC-07](../activity/AD-UC-07.md) | Depends on confirmation of BR-03-01. “Once” is specified in AD-UC-07. |
| BR-03-03 | Send one closed notice only if the first notice for that alert was sent. | UC-07 | FR-03-10; ADR-005; [AD-UC-07](../activity/AD-UC-07.md) | Documented in ADR-005 and AD-UC-07; align SyR-06 wording. |
| BR-03-04 | If the resident has no linked family member, skip the family push notice. | UC-07 | FR-03-11; [AD-UC-07](../activity/AD-UC-07.md) | Stated in the activity diagram. |
| BR-03-05 | If a family push notice fails, retry it up to the configured retry limit and log the failure when the limit is reached. retry up to 3 times, then log the failure (the interval is still open) | UC-07 | FR-03-11; [AD-UC-07](../activity/AD-UC-07.md), open questions 5–6 | Needs confirmation: AD-UC-07 assumes three retries and a one-minute interval. |
| BR-03-06 | Notify nurses again if an alert is not acknowledged within one minute; count the initial notice as one of three notices. If the alert is still unacknowledged after the third notice, log the failure and keep the alert Escalated. | UC-06 | FR-03-02–FR-03-05; [AD-UC-06](../activity/AD-UC-06.md) | Confirm the notice count and post-failure handling against the open questions in AD-UC-06. |
| BR-03-07 | Send family notices to all family members linked to the resident. | UC-07 | [AD-UC-07](../activity/AD-UC-07.md), acceptance tests and open question 4 | Needs confirmation: currently an assumption. |
| BR-04-01 | A family member may view a resident's condition only when the system verifies that the resident is linked to that family member's account. | UC-08 | FR-04-01; [AD-UC-08](../activity/AD-UC-08.md) | Access check is required; confirm the exact verification rule. |
| BR-06-01 | Check the user's session token and role permissions on every request. | All authenticated use cases | ADR-003; [architecture](../architecture/architecture.md) | Accepted architecture decision. |
| BR-06-02 | An Admin may assign only roles within the Admin's permitted scope. Replacing an account's role also replaces its permissions; permissions from the previous role shall no longer apply. | UC-12 | FR-06-02, FR-06-03; [AD-UC-12](../activity/AD-UC-12.md) | Stated in the activity diagram; confirm against approved W1 requirements. |
| BR-06-03 | Only an Active account may be selected for role assignment. | UC-12 | [AD-UC-12](../activity/AD-UC-12.md), precondition and open question 3 | Needs confirmation: currently the diagram's default. |

