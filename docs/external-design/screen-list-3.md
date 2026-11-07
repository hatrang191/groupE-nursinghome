# Screen List: Dien Hong System

> Scope: every actor with a user interface (Receptionist, Nurse, Residents' Family, BOD, Admin).
> The Radar is a device and has no screen.

Screen ID pattern: `S-<feature>-<nn>`. Shared screens use `S-F00-<nn>`.

| Screen ID | Screen name | Actor | Feature | Shown when (purpose) | Entry points | Use cases / FRs served | Priority | Screen spec |
|---|---|---|---|---|---|---|---|---|
| S-F00-01 | Sign in | Receptionist, Nurse, Residents' Family, BOD, Admin | F-00 | First visit, or the session has ended | Any link while signed out | (platform) · FR-00-01 | Must | `screens/screen-spec-S-F00-01.md` |
| S-F01-01 | Resident list | Receptionist, Nurse | F-01 | Home of the receptionist and the nurse: the user picks the resident to work on; the receptionist also starts a new resident | Home after sign in (receptionist, nurse); home after an access-denied refusal; menu | UC-01, UC-02, UC-03, UC-04, UC-05 · FR-00-01, FR-06-03 | Must | `screens/screen-spec-S-F01-01.md` |
| S-F01-02 | Resident profile | Receptionist, Nurse | F-01 | User reads the resident's current profile information, including the resident ID after a profile is created or updated | S-F01-01; success state of S-F01-03 | UC-01, UC-02, UC-03 · FR-01-01, FR-01-02, FR-01-03 | Must | `screens/screen-spec-S-F01-02.md` |
| S-F01-03 | Resident form | Receptionist | F-01 | Receptionist enters a new resident's details, or edits the information of an existing resident, and saves | S-F01-01 ("New resident"); S-F01-02 ("Edit") | UC-01, UC-02 · FR-01-01, FR-01-02 | Must | `screens/screen-spec-S-F01-03.md` |
| S-F02-01 | Condition history | Nurse | F-02 | Nurse reads all condition records of the resident in time order | S-F01-01; S-F01-02 | UC-04 · FR-02-04 | Should | `screens/screen-spec-S-F02-01.md` |
| S-F02-02 | Update condition | Nurse | F-02 | Nurse enters the resident's condition and saves it; the resident's name and room are shown | S-F01-01; S-F01-02; S-F02-01 | UC-05 · FR-02-01, FR-02-02 | Must | `screens/screen-spec-S-F02-02.md` |
| S-F03-01 | Alerts | Nurse | F-03 | Nurse sees the open alerts (New, Escalated, Acknowledged), newest first; acknowledges one, or reports the outcome after handling it | Menu; "New alert" pop-up; push notice on the phone | UC-06 · FR-03-02, FR-03-03, FR-03-06 | Must | `screens/screen-spec-S-F03-01.md` |
| S-F03-02 | Alert notices | Residents' Family | F-03 | Family member reads the notice of a dangerous alert for their resident and, later, the closed notice with the outcome | Push notice on the phone; menu | UC-07 · FR-03-09, FR-03-10 | Must | `screens/screen-spec-S-F03-02.md` |
| S-F04-01 | Family chat | Nurse | F-04 | Nurse reads the conversation with a resident's family and sends a message | S-F01-02; S-F02-01; menu | UC-09 · FR-04-03, FR-04-04 | Should | `screens/screen-spec-S-F04-01.md` |
| S-F04-02 | Resident condition | Residents' Family | F-04 | Home of the family member: selects one of their linked residents and reads the current condition | Home after sign in (family); menu | UC-08 · FR-04-01, FR-04-02 | Must | `screens/screen-spec-S-F04-02.md` |
| S-F04-03 | Chat with nurses | Residents' Family | F-04 | Family member reads the conversation with the nurses and sends a message | S-F04-02; menu | UC-09 · FR-04-03, FR-04-04 | Should | `screens/screen-spec-S-F04-03.md` |
| S-F05-01 | Reports | BOD | F-05 | Home of the BOD: selects a period and sees the number of alerts, the average time from alert to acknowledgement and the number of escalated alerts | Home after sign in (BOD) | UC-10 · FR-05-01 | Could | `screens/screen-spec-S-F05-01.md` |
| S-F06-01 | Accounts | Admin | F-06 | Home of the Admin: chooses an operation (create, edit, deactivate, assign role) and selects an Active account | Home after sign in (admin); menu | UC-11, UC-12 · FR-06-01, FR-06-03, FR-06-06, FR-06-07 | Must | `screens/screen-spec-S-F06-01.md` |
| S-F06-02 | Account form | Admin | F-06 | Admin enters a new account (with the residents to link for a family account), or changes the name, contact information or password of an Active account | S-F06-01 ("Create account", "Edit") | UC-11 · FR-06-01, FR-06-04, FR-06-05, FR-06-06 | Must | `screens/screen-spec-S-F06-02.md` |
| S-F06-03 | Assign role | Admin | F-06 | Admin sees the account's current role and the roles they may assign, selects one and confirms | S-F06-01 | UC-12 · FR-06-02, FR-06-03 | Must | `screens/screen-spec-S-F06-03.md` |
| S-F06-04 | Radar status | Admin | F-06 | Admin sees every registered radar with its status (Online or Offline) and the time of its last data; Offline radars are highlighted | Menu | UC-13 · FR-06-09, FR-06-10, FR-06-11 | Could | `screens/screen-spec-S-F06-04.md` |
| S-F06-05 | Access and action logs | Admin | F-06 | Admin lists access and action entries, filtered by user and date | Menu; S-F06-04 | UC-14 · FR-06-12 | Could | `screens/screen-spec-S-F06-05.md` |

## Modals and overlays

| Modal | Opened from screen | Purpose |
|---|---|---|
| New alert (pop-up with sound) | Any signed-in nurse screen: S-F01-01, S-F01-02, S-F02-01, S-F02-02, S-F03-01, S-F04-01 (opened by the system) | Show the resident, location and time of a new or repeated alert; Acknowledge (FR-03-02, FR-03-03, FR-03-04) |
| Close alert | S-F03-01 | Enter the outcome of an acknowledged alert (real event or false alarm) and close it (FR-03-06) |

## What is deliberately not a screen

| Candidate | Why it is not a screen |
|---|---|
| "Sign-in refused" | Error state of S-F00-01 (FR-00-01) |
| "Access denied" | Message on the role's home screen after a refused request (FR-06-03), and on S-F04-02 for a resident not linked to the family account (FR-04-01) |
| "Validation errors" on a resident | Error state of S-F01-03; the receptionist corrects and saves again (FR-01-01, FR-01-02) |
| "Condition rejected" | Error state of S-F02-02; the nurse corrects and saves again (FR-02-01) |
| "No condition recorded yet" | Empty state of S-F04-02 (FR-04-02) |
| Acknowledge alert | Action on the "New alert" pop-up and on S-F03-01 (FR-03-03-FE) |
| Deactivate account | Action on S-F06-01 (FR-06-07-FE) |
| "Account errors", "Operation complete" | Error state of S-F06-02, where the Admin corrects or cancels (FR-06-04); confirmation after create, edit or deactivate (AD-UC-11 S4) |
| "Role outside the permitted scope" | Error state of S-F06-03; the Admin chooses again (FR-06-03) |
| "Radar status cannot be shown" | Error state of S-F06-04 (FR-06-11) |
| Push notices to nurse and family phones | Sent by the Browser push service outside the web app (FR-03-02, FR-03-09, FR-03-10, ADR-005); they only lead into S-F03-01 or S-F03-02 |
| Receive radar event, group repeated events, always on (FR-03-01, FR-03-07, FR-03-08) | Done by the Radar Gateway and the API Server (ADR-002), no user interface |
| Notify nurses again, log failed alert (FR-03-04, FR-03-05) | Time-triggered by the Scheduler (ADR-004); the nurse only sees the "New alert" pop-up again |
| Skip or retry family push (FR-03-11) | Background rule of UC-07, no user interface |
| Radar connection checks (FR-06-09-INT) | Done by the Radar Gateway at a fixed interval; S-F06-04 only reads the result |
| Keep condition history, record action and access logs (FR-02-03, FR-06-08, FR-06-13) | Storage rules, no user interface; their records are listed on S-F02-01 and S-F06-05 |
