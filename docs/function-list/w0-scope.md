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
| UC-06       | Receive alert                               | Nurse                    | F-03             | Yes            | Must     |
| UC-07       | Receive alert notice                        | Residents' Family        | F-03             | Yes            | Must     |
| UC-08       | View resident's condition                   | Residents' Family        | F-02             | Yes            | Must     |
| UC-09       | Exchange messages between family and nurses | Nurse, Residents' Family | F-04             | Yes            | Must     |
| UC-10       | View reports                                | BOD                      | F-05             | Yes            | Could    |
| UC-11       | Set up accounts                             | Admin                    | F-06             | Yes            | Must     |
| UC-12       | Assign role-based permissions               | Admin                    | F-06             | Yes            | Must     |
| UC-13       | Monitor radar status                        | Admin                    | F-06             | Yes            | Should   |
| UC-14       | View access and action logs                 | Admin                    | F-06             | Yes            | Could    |

UC-07 was added after the Lesson 7 Inputs check (see [activity-index](../activity/activity-index.md), row 1).

## Table 2. Feature list (flows into W1)

| Feature ID | Feature name (business noun) | Scope in one sentence                                                                                                          | Primary actors served                  | Owner                  | Use cases covered          |
|------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------|----------------------------------------|------------------------|----------------------------|
| F-01       | Resident Profile Management  | Staff create, update and view resident profiles so every resident has accurate, up-to-date basic information.                  | Receptionist, Nurse                    | Nguyễn Thị Trúc Anh    | UC-01, UC-02, UC-03        |
| F-02       | Condition Tracking           | Nurses record each resident's condition and review its history over time.                                                      | Nurse                                  | Nguyễn Hà Linh Trang   | UC-04, UC-05, UC-08        |
| F-03       | Abnormal Alert Handling      | The radar system detects abnormal events and sends alerts that nurses (web) and families (mobile app) receive immediately.     | Radar system, Nurse, Residents' Family | Lê Hà Thanh            | UC-06, UC-07               |
| F-04       | Family Communication         | Families follow their relative's condition and chat directly with nurses through the mobile app.                               | Residents' Family, Nurse               | Nguyễn Trần Khánh Linh | UC-09                      |
| F-05       | Dashboard & Reporting        | The Board of Directors views summary reports on care and operations.                                                           | BOD                                    | Trần Lê Bảo An         | UC-10                      |
| F-06       | System Administration        | Admins set up accounts, assign role-based permissions, monitor radar devices and audit access and action logs.                 | Admin                                  | Lê Thị Thùy Linh       | UC-11, UC-12, UC-13, UC-14 |

Feature count: 6 (allowed 3 to 6): PASS. Coverage against the CVCA: all primary actors (Receptionist, Nurse, Residents' Family, BOD, Admin) are served.

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

| BR    | Rule          |
|-------|---------------|
| BR-01 | (to be added) |
