# Activity Diagram Index: Dien Hong System

## Inputs check

| # | Finding | Source to fix | Action | Owner | Status |
|---|---|---|---|---|---|
| 1 | Nurse receives and handles alerts but is not connected to any use case | Use Case Diagram | Renamed UC-06 to "Send and acknowledge alert" and connected both Radar and Nurse | Nguyen Ha Linh Trang | Done |
| 2 | Browser push service sends notifications but was missing from the Context Diagram | Context Diagram | Added "Browser push service" as an external entity | Le Thi Thuy Linh | Done |
| 3 | BOD flow is in the wrong direction: BOD only sends "view report", while the system → BOD direction says "(none)" | Context Diagram | Added "reports" in the system → BOD direction | Le Thi Thuy Linh | Done |
| 4 | Nurse flows are missing alert data in both directions | Context Diagram | Added "alerts" (system → Nurse), "alert acknowledgement" and "alert closing" (Nurse → system) | Le Thi Thuy Linh | Done |
| 5 | The "alert rules" flow from System admin has no matching use case | Context Diagram | Removed the "alert rules" flow | Nguyen Tran Khanh Linh | Done |
| 6 | Family sends "messages" for UC-09, but the Context Diagram only has "care requests" | Context Diagram | Unified the flow name as "messages" | Nguyen Tran Khanh Linh | Done |
| 7 | The flow name "audit logs" does not match UC-14 | Context Diagram | Renamed to "access and action logs" | Nguyen Tran Khanh Linh | Done |
| 8 | The flows "handover notes", "announcements" and "backup/restore" have no use case | Context Diagram | Removed these flows (out of scope) | Nguyen Ha Linh Trang | Done |
| 9 | Family is only connected to Receptionist; the flows DH → Family (alerts, status) and Family → DH (messages, requests) are missing | CVCA | Added both flows | Nguyen Ha Linh Trang | Done |
| 10 | The Context Diagram says "built from CVCA", but the CVCA has no Admin | CVCA | Added Admin inside the Nursing House box | Le Ha Thanh | Done |
| 11 | Names are inconsistent across the 3 files: Admin / System admin, Radar / Radar, Receptionist - Accountant / Receptionist | All 3 files | Use "Admin" in all 3 files; pick one name each for Radar and Receptionist; update the Change log to v2 | Le Ha Thanh | Done |

Use cases

| Use case | Name | Feature | Priority | Activity diagram | Status | Owner | Reason if not needed |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| UC-01 | Create new resident profile | F-01 | Must | `AD-UC-01.md` | Reviewed | Le Ha Thanh |  |
| UC-02 | Update resident information | F-01 | Should | `AD-UC-02.md` | Reviewed | Nguyen Thi Truc Anh |  |
| UC-03 | View resident's profile | F-01 | Should | not needed |  |  | Single display step (FR-01-03); no decision, no external system, no time trigger |
| UC-04 | View condition history | F-02 | Should | not needed |  |  | Single display step (FR-02-04); no decision, no external system, no time trigger |
| UC-05 | Update condition | F-02 | Must | `AD-UC-05.md` | Reviewed | Nguyen Tran Khanh Linh |  |
| UC-06 | Send and acknowledge alert | F-03 | Must | `AD-UC-06.md` | Reviewed | Nguyen Ha Linh Trang |  |
| UC-07 | Receive alert notice | F-03 | Must | `AD-UC-07.md` | Reviewed | Nguyen Ha Linh Trang |  |
| UC-08 | View resident's condition | F-04 | Should | `AD-UC-08.md` | Reviewed | Nguyen Tran Khanh Linh |  |
| UC-09 | Exchange messages between family and nurses | F-04 | Should | `AD-UC-09.md` | Reviewed | Tran Le Bao An |  |
| UC-10 | View reports | F-05 | Could | not needed |  |  | This is a query and display operation, with no IF statements. |
| UC-11 | Set up accounts | F-06 | Must | `AD-UC-11.md` | Reviewed | Le Thi Thuy Linh |  |
| UC-12 | Assign role-based permissions | F-06 | Must | `AD-UC-12.md` | Reviewed | Le Thi Thuy Linh |  |
| UC-13 | Monitor radar status | F-06 | Could | `AD-UC-13.md` | Reviewed | Le Thi Thuy Linh |  |
| UC-14 | View access and action logs | F-06 | Could | not needed |  |  | This is a query and display operation, with no IF statements. |

Selection basis for the use cases that get a diagram:

| Use case | Reason it needs a diagram | Source |
| :---- | :---- | :---- |
| UC-01 | Decision: IF the information is invalid, return the validation errors; Receptionist lane plus system lane | FR-01-01 |
| UC-02 | Receptionist lane plus system lane; decision: IF the changed information is invalid, display the validation errors and let the receptionist edit again; every valid change is stored with the time and the staff name | FR-01-02 |
| UC-05 | Nurse lane plus system lane; every update is stored as a new record, never overwritten | FR-02-01, FR-02-02, FR-02-03 |
| UC-06 | External lane (Radar Monitoring System) starts the flow; the alert is stored with severity, location and time | FR-03-01, FR-03-02, FR-03-03, FR-03-04, FR-03-05, FR-03-06, FR-03-07, FR-03-08 |
| UC-07 | External lane (Radar) starts the flow, plus system lane and Residents' Family lane; decisions: only a dangerous alert (BR-03-01) gets a first notice, IF the resident has no linked family member skip the push, IF the push fails retry then log; one more notice when the alert is Closed | FR-03-09, FR-03-10, FR-03-11 |
| UC-08 | Decision: IF the family member's access to that resident cannot be verified, deny the request | FR-04-01 |
| UC-09 | Three lanes (Residents' Family, Dien Hong System, Nurses); decision: who starts the message (family member or nurse); a loop allows further messages in the same conversation | FR-04-03, FR-04-04 |
| UC-11 | Admin lane plus system lane; the account is saved with name, role and credentials | FR-06-01, FR-06-03, FR-06-04, FR-06-05, FR-06-06, FR-06-07, FR-06-08 |
| UC-12 | Decisions: Admin may assign roles; the requested role is within the Admin's permissions | FR-06-02, FR-06-03, FR-06-08 |

Lane names:

| Role in the diagrams | Lane name |
| :---- | :---- |
| Receptionist | Receptionist |
| Nurses | Nurses |
| Residents' Family | Residents' Family |
| Board of directors | BOD |
| Admin | Admin |
| Radar | Radar |
| The whole system | Dien Hong System |

&nbsp;

| Object | States |
| :---- | :---- |
| Account | Active (after S3 in AD-UC-11), Invalid (after S2), Rejected (after S1) |
| Role | Authorized (after S2 in AD-UC-12), Unauthorized (after S1) |
| Resident Condition Record | Saved (written in AD-UC-05, read in AD-UC-08) |
| Alert | New, Acknowledged, Escalated, Closed (written in AD-UC-06; AD-UC-07 reads New and Closed) |
| Family notice | First sent, Closed sent (AD-UC-07) |
| Resident Profile | none: no state is defined in Release 1 (AD-UC-01, AD-UC-02) |

&nbsp;
