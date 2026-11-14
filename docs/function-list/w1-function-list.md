# W1 Function List: Dien Hong System

Lesson 5 (block W1). L2 = a business behaviour the client signs off, with its EARS criterion and function-point type. L3 = one work item in one layer (FE, BE, INT), sized S = 4 h, M = 8 h, L = 16 h. Initial priority is set here; the effective priority comes from [W4](w4-priorities.md).

Sources swept, one diagram per pass: [Use Case](../use-case/use-case-diagram.md), Sequence, [Context](../context/context-diagram.md), [CVCA](../context/cvca.md), [Architecture](../architecture/architecture.md) + ADR. Features come from [W0](w0-scope.md); activity diagrams are listed in the [activity index](../activity/activity-index.md).

## L2 functions

| Feature | L2 ID | L2 function name | Actor | EARS acceptance criterion | Source trace | Initial priority | FP type |
|---|---|---|---|---|---|---|---|
| F-00 | FR-00-01 | Sign in | Receptionist, Nurse, BOD, Admin, Residents' Family | WHEN a user submits the identifier and password of an Active account, the system SHALL start a session and open the home screen of the user's role. IF the identifier or password is wrong or the account is Inactive, THEN the system SHALL refuse the sign-in and show a message. | Context: all user entities; precondition of every activity diagram; FR-06-03 | Must | EQ |
| F-01 | FR-01-01 | Create new resident profile | Receptionist | WHEN a receptionist submits a new resident's details, the system SHALL validate them and, if they are valid, generate a unique resident ID, save the resident profile and show it. IF the details are invalid, THEN the system SHALL NOT save the profile and SHALL display the validation errors so the receptionist can correct them. | UC:UC-01; SEQ:SD-01; BR-01-01, BR-01-02 | Must | EI |
| F-01 | FR-01-02 | Update resident information | Receptionist | WHEN a receptionist saves valid changes to a resident profile, the system SHALL store the changes with the time and the staff name and show the updated information. IF the changes are invalid, THEN the system SHALL NOT save them and SHALL display the validation errors so the receptionist can edit again. | UC:UC-02; BR-01-02 | Should | EI |
| F-01 | FR-01-03 | View resident's profile | Nurse | WHEN a nurse opens a resident, the system SHALL display the resident's current profile information. | UC:UC-03 | Must | EQ |
| F-02 | FR-02-01 | Validate condition | System | IF a nurse saves a condition that is invalid, such as a blank condition, THEN the system SHALL reject the save, leave earlier records unchanged, tell the nurse why, and let the nurse correct the condition and save it again. | UC: UC-05 | Must | NC |
| F-02 | FR-02-02 | Update condition | Nurse | WHEN a nurse selects an existing resident, enters valid resident's condition and saves, the system SHALL store it for that resident with the time and the nurse's name. | UC: UC-05 | Must | EI |
| F-02 | FR-02-03 | Keep condition history (non-functional) | System | The system SHALL keep every condition update as a separate time-stamped record and SHALL NOT overwrite or delete earlier records. | UC: UC-05; BR-02-01 | Must | NC |
| F-02 | FR-02-04 | View condition history | Nurse | WHEN a nurse opens the condition history of a resident, the system SHALL list all condition records in time order. | UC: UC-04 | Should | EQ |
| F-03 | FR-03-01 | Receive abnormal alert from radar | Radar | WHEN the radar reports an abnormal event, the system SHALL create an alert for the resident in that room within 5 seconds. | UC: UC-06 | Must | EI |
| F-03 | FR-03-02 | Receive alert (nurse) | Nurse | WHEN a new alert is created, the system SHALL show a pop-up with the resident, location and time on every signed-in nurse screen and SHALL send a push notice to the phone of every nurse on duty. | UC: UC-06 | Must | EO |
| F-03 | FR-03-03 | Acknowledge alert | Nurse | WHEN a nurse selects Acknowledge on an alert, the system SHALL mark the alert as acknowledged and stop it showing as unattended. | UC: UC-06 | Must | EI |
| F-03 | FR-03-04 | Notify nurses again when nobody acknowledges | System | IF no nurse acknowledges an alert within 1 minute after a notice and fewer than 3 notices have been sent, THEN the system SHALL notify the nurses on duty again, add 1 to the notice count and set the alert to Escalated. | UC: UC-06; BR-03-06 | Must | NC |
| F-03 | FR-03-05 | Log failed alert | System | IF no nurse acknowledges an alert within 1 minute after the third notice, THEN the system SHALL log the failure of that alert and SHALL send no more notices. | UC: UC-06; BR-03-06 | Must | NC |
| F-03 | FR-03-06 | Close alert with outcome | Nurse | WHEN a nurse reports the outcome of an acknowledged alert, such as a real event or a false alarm, the system SHALL save the outcome and set the alert to Closed. | UC: UC-06 | Must | EI |
| F-03 | FR-03-07 | Group repeated radar events | System | WHEN the radar sends repeated events for the same resident within 1 minute, the system SHALL merge them into one alert. | UC: UC-06 | Should | NC |
| F-03 | FR-03-08 | Always on (non-functional) | System | The system SHALL receive and deliver alerts 24 hours a day and SHALL restart automatically after a failure. | UC: UC-06 | Must | NC |
| F-03 | FR-03-09 | Receive alert notice (family) | Residents' Family | WHEN a new alert is dangerous (BR-03-01: severity High) and its resident has at least one linked family member, the system SHALL send one first notice to every linked family account as a push notice to their phone. | UC: UC-07; BR-03-01, BR-03-02, BR-03-07 | Should | EO |
| F-03 | FR-03-10 | Send closed notice to family | Residents' Family | WHEN an alert whose first notice was delivered is closed, the system SHALL send one closed notice with the outcome to the linked family accounts. | UC: UC-07; BR-03-03 | Could | EO |
| F-03 | FR-03-11 | Skip or retry family push | System | IF the resident of a dangerous alert has no linked family member, THEN the system SHALL skip the push. IF a push to the family fails, THEN the system SHALL retry it up to 3 times and then log the failure. | UC: UC-07; BR-03-04, BR-03-05 | Should | NC |
| F-04 | FR-04-01 | Verify family access to resident (non-functional) | System | IF the family member's access to the selected resident cannot be verified, THEN the system SHALL deny the request with an access-denied message. | UC: UC-08; BR-04-01 | Must | NC |
| F-04 | FR-04-02 | View resident's condition | Residents' Family | WHEN a family member selects one of their linked residents, the system SHALL display the current condition of that resident. IF no condition has been recorded for that resident, THEN the system SHALL tell the family member that no condition has been recorded yet. | UC: UC-08; BR-02-02 | Should | EQ |
| F-04 | FR-04-03 | Chat with nurse | Residents' Family | WHEN a family member sends a message, the system SHALL deliver it to the nurse on duty and store it. | UC: UC-09 | Should | EI |
| F-04 | FR-04-04 | Chat with family | Nurse | WHEN a nurse sends a message to a family member, the system SHALL deliver it to that family account and store it. | UC: UC-09 | Should | EI |
| F-05 | FR-05-01 | View report | BOD | WHEN a member of the Board of Directors selects a period, the system SHALL display for that period the number of alerts, the average time from alert to acknowledgement, and the number of alerts that were escalated. | UC: UC-10 | Could | EO |
| F-06 | FR-06-01 | Set up account | Admin | WHEN an admin submits a new account with a role, the system SHALL create the account and enable sign-in. | UC: UC-11 | Must | EI |
| F-06 | FR-06-02 | Assign role-based permissions | Admin | WHEN an admin assigns a role to an account, the system SHALL grant the permissions of that role. | UC: UC-12; BR-06-02, BR-06-03 | Must | EI |
| F-06 | FR-06-03 | Enforce role-based access (non-functional) | System | IF a user requests a function that their role does not allow, THEN the system SHALL refuse it and return the user to the home screen of their role with an access-denied message. | UC: UC-11; UC-12; UC-13; BR-06-01 | Must | NC |
| F-06 | FR-06-04 | Validate and correct account information | System | IF the information of a new or edited account is incomplete or invalid, including a family account with no selected resident, or its identifier is already used by another account, THEN the system SHALL NOT save it and SHALL report each error. WHEN the errors have been reported, the system SHALL let the admin correct the information and submit it again, or cancel the operation without saving. | UC: UC-11 | Must | NC |
| F-06 | FR-06-05 | Link family account to resident | Admin | WHEN an admin creates an account for a family member and selects one or more residents, the system SHALL save the link between the account and each selected resident. | UC: UC-11 | Must | EI |
| F-06 | FR-06-06 | Edit account information | Admin | WHEN an admin submits valid changes to the name, contact information or password of an Active account, the system SHALL save the changes and SHALL keep the role and linked residents of the account unchanged. | UC: UC-11 | Should | EI |
| F-06 | FR-06-07 | Deactivate account | Admin | WHEN an admin deactivates an Active account, the system SHALL set the account to Inactive, SHALL end every open session of the account, SHALL reject any later sign-in or request made with it, and SHALL keep the account data and history. | UC: UC-11 | Must | EI |
| F-06 | FR-06-08 | Record account and role changes in the action log (non-functional) | System | WHEN an account is created, edited or deactivated, or its role is changed, the system SHALL record who did it, which account, what was done and when. | UC: UC-11; UC-12 | Should | NC |
| F-06 | FR-06-09 | Show radar status | Admin | WHEN an admin requests the radar status, the system SHALL show every registered radar with its status (Online or Offline) and the time of its last data. | UC: UC-13 | Must | EQ |
| F-06 | FR-06-10 | Highlight Offline radars | Admin | WHEN a radar is Offline, the system SHALL highlight it among the radars shown. | UC: UC-13 | Could | NC |
| F-06 | FR-06-11 | Report radar status unavailable | Admin | IF the system cannot obtain the radar status, or no radar is registered, THEN the system SHALL tell the admin that the status cannot be shown and SHALL NOT show an old status as the current one. | UC: UC-13 | Could | NC |
| F-06 | FR-06-12 | View access and action logs | Admin | WHEN an admin opens the logs, the system SHALL list access and action entries with filters by user and date. | UC: UC-14 | Could | EQ |
| F-06 | FR-06-13 | Record access and action logs (non-functional) | System | WHEN a user signs in or performs an action, the system SHALL record the user, the action and the time. | UC: UC-14 | Should | NC |

## L3 work items

| L3 ID | L2 ID | Layer | L3 work item | Size | Base hours | EARS precise? | Hidden work? |
|---|---|---|---|---|---|---|---|
| FR-00-01-FE | FR-00-01 | FE | Screen: sign-in for every user (one responsive screen for computer and phone, ADR-001) | S | 4 | Yes | No |
| FR-00-01-BE | FR-00-01 | BE | API: check the account and password, start a session, refuse Inactive accounts | S | 4 | Yes | No |
| FR-01-01-FE | FR-01-01 | FE | Form: create resident profile | S | 4 | Yes | No |
| FR-01-01-BE | FR-01-01 | BE | API: validate and create resident profile | S | 4 | Yes | No |
| FR-01-02-FE | FR-01-02 | FE | Form: edit resident profile | S | 4 | Yes | No |
| FR-01-02-BE | FR-01-02 | BE | API: update resident profile | S | 4 | Yes | No |
| FR-01-03-FE | FR-01-03 | FE | Screen: resident profile detail | S | 4 | Yes | No |
| FR-01-03-BE | FR-01-03 | BE | API: read resident profile | S | 4 | Yes | No |
| FR-02-01-BE | FR-02-01 | BE | Validation rules for condition values (the condition can not be blank) | S | 4 | Yes | No |
| FR-02-02-FE | FR-02-02 | FE | Form: update resident condition, with the validation message (FR-02-01) | S | 4 | Yes | No |
| FR-02-02-BE | FR-02-02 | BE | API: save condition update | S | 4 | Yes | No |
| FR-02-03-BE | FR-02-03 | BE | Append-only condition history storage | S | 4 | Yes | Yes |
| FR-02-04-FE | FR-02-04 | FE | Screen: condition history timeline | S | 4 | Yes | No |
| FR-02-04-BE | FR-02-04 | BE | API: read condition history | S | 4 | Yes | No |
| FR-03-01-INT | FR-03-01 | INT | Receive abnormal events from the radar system | L | 16 | Yes | No |
| FR-03-01-BE | FR-03-01 | BE | Map radar event to resident and create alert | S | 4 | Yes | No |
| FR-03-02-FE | FR-03-02 | FE | Screen: real-time alert pop-up and sound | S | 4 | Yes | No |
| FR-03-02-BE | FR-03-02 | BE | Real-time push of alert to nurse screens and push notice to nurses' phones (ADR-005) | M | 8 | Yes | No |
| FR-03-03-FE | FR-03-03 | FE | Action: acknowledge alert | S | 4 | Yes | No |
| FR-03-03-BE | FR-03-03 | BE | API: change alert state to acknowledged | S | 4 | Yes | No |
| FR-03-04-BE | FR-03-04 | BE | Scheduled job: re-send unacknowledged alerts | S | 4 | Yes | No |
| FR-03-05-BE | FR-03-05 | BE | Log alerts still unacknowledged after the third notice | S | 4 | Yes | No |
| FR-03-06-FE | FR-03-06 | FE | Form: close alert with outcome | S | 4 | Yes | No |
| FR-03-06-BE | FR-03-06 | BE | API: close alert and store outcome | S | 4 | Yes | No |
| FR-03-07-BE | FR-03-07 | BE | Rule: merge repeated events in a time window | M | 8 | Yes | No |
| FR-03-08-INT | FR-03-08 | INT | Health check, monitoring and automatic restart | M | 8 | Yes | Yes |
| FR-03-09-FE | FR-03-09 | FE | Phone screen: alert notice and closed notice with outcome | S | 4 | Yes | No |
| FR-03-09-BE | FR-03-09 | BE | Check severity (BR-03-01), select linked family accounts and trigger the first notice once per alert | S | 4 | Yes | No |
| FR-03-09-INT | FR-03-09 | INT | Push notification service integration for nurses and families (ADR-005) | M | 8 | No | No |
| FR-03-10-BE | FR-03-10 | BE | Trigger closed notice on alert closure | S | 4 | Yes | No |
| FR-03-10-INT | FR-03-10 | INT | Send closed notice through push service | S | 4 | Yes | No |
| FR-03-11-BE | FR-03-11 | BE | Rule: skip push without linked family; retry a failed push, then log | S | 4 | Yes | No |
| FR-04-01-FE | FR-04-01 | FE | Message: access denied (family screens) | S | 4 | Yes | No |
| FR-04-01-BE | FR-04-01 | BE | Check family-to-resident link on every request | S | 4 | Yes | Yes |
| FR-04-02-FE | FR-04-02 | FE | Screen: resident condition (family) | S | 4 | Yes | No |
| FR-04-02-BE | FR-04-02 | BE | API: read condition for linked resident | S | 4 | Yes | No |
| FR-04-03-FE | FR-04-03 | FE | Screen: chat with nurse (family) | S | 4 | Yes | No |
| FR-04-03-BE | FR-04-03 | BE | API and real-time delivery of messages | M | 8 | Yes | No |
| FR-04-04-FE | FR-04-04 | FE | Screen: nurse chat with family | S | 4 | Yes | No |
| FR-04-04-BE | FR-04-04 | BE | API: send and store nurse message | S | 4 | Yes | No |
| FR-05-01-FE | FR-05-01 | FE | Screen: report with period filter | M | 8 | Yes | No |
| FR-05-01-BE | FR-05-01 | BE | Aggregation query for report totals | S | 4 | Yes | No |
| FR-06-01-FE | FR-06-01 | FE | Form: create account | S | 4 | Yes | No |
| FR-06-01-BE | FR-06-01 | BE | API: create account | S | 4 | Yes | No |
| FR-06-02-FE | FR-06-02 | FE | Screen: assign role to account | S | 4 | Yes | No |
| FR-06-02-BE | FR-06-02 | BE | API: save role assignment | S | 4 | Yes | No |
| FR-06-03-BE | FR-06-03 | BE | Role model and permission check on every endpoint | L | 16 | Yes | Yes |
| FR-06-04-FE | FR-06-04 | FE | Inline validation messages on account form | S | 4 | Yes | No |
| FR-06-04-BE | FR-06-04 | BE | Rules: validate and detect duplicate accounts | S | 4 | Yes | No |
| FR-06-05-FE | FR-06-05 | FE | Resident selector on the create-account form | S | 4 | Yes | No |
| FR-06-05-BE | FR-06-05 | BE | API: save family-resident link | S | 4 | Yes | No |
| FR-06-06-FE | FR-06-06 | FE | Form: edit account | S | 4 | Yes | No |
| FR-06-06-BE | FR-06-06 | BE | API: update account | S | 4 | Yes | No |
| FR-06-07-FE | FR-06-07 | FE | Action: deactivate account | S | 4 | Yes | No |
| FR-06-07-BE | FR-06-07 | BE | API: deactivate account and end sessions | S | 4 | Yes | No |
| FR-06-08-BE | FR-06-08 | BE | Write audit entry on account and role changes | S | 4 | Yes | Yes |
| FR-06-09-FE | FR-06-09 | FE | Screen: radar status list | S | 4 | Yes | No |
| FR-06-09-BE | FR-06-09 | BE | Work out each radar's Online/Offline status from the time of its last successful check or data (Offline after 5 minutes without a response); mark the status unavailable when it cannot be obtained (FR-06-11) | S | 4 | Yes | No |
| FR-06-09-INT | FR-06-09 | INT | Gateway/server actively checks each radar's connection at a fixed interval and records the time of the last successful check and of the last data received | S | 4 | Yes | No |
| FR-06-10-FE | FR-06-10 | FE | Highlight offline radars | S | 4 | Yes | No |
| FR-06-11-FE | FR-06-11 | FE | Show status-unavailable message | S | 4 | Yes | No |
| FR-06-12-FE | FR-06-12 | FE | Screen: log list with filters | S | 4 | Yes | No |
| FR-06-12-BE | FR-06-12 | BE | API: query logs | S | 4 | Yes | No |
| FR-06-13-BE | FR-06-13 | BE | Record sign-in and user actions | S | 4 | Yes | Yes |

37 L2 functions, 64 L3 work items, 304 base hours. No row is above the 16-hour ceiling.

NC rows (FR-02-01, FR-02-03, FR-03-04, FR-03-05 ...) score zero function points but carry real work: that gap is why the function-point count (96 points) is only a cross-check.
