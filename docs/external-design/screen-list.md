# Screen List: Dien Hong System

> Scope: every actor with a user interface (Receptionist, Nurse, Residents' Family, BOD, Admin).
> The Radar is a device and has no screen.

Screen ID pattern: `S-<feature>-<nn>`. Shared screens use `S-F00-<nn>`.

| Screen ID | Screen name | Actor | Feature | Shown when (purpose) | Entry points | Use cases / FRs served | Priority | Screen spec |
|---|---|---|---|---|---|---|---|---|
| S-F00-01 | Sign in | Receptionist, Nurse, Residents' Family, BOD, Admin | F-00 | First visit, or the session has ended | Any link while signed out | (platform) · FR-00-01 | Must | `screens/screen-spec-S-F00-01.md` |
| S-F01-01 | Resident list | Receptionist, Nurse | F-01 | Home of the receptionist and the nurse: the user picks the resident to work on; the receptionist also starts a new resident | Home after sign in (receptionist, nurse); home after an access-denied refusal; menu | UC-01, UC-02, UC-03, UC-04, UC-05 · FR-02-02 | Must | `screens/screen-spec-S-F01-01.md` |
| S-F01-02 | Resident profile detail | Receptionist, Nurse | F-01 | User reads the resident's current profile information, including the resident ID after a profile is created or updated | S-F01-01; success state of S-F01-03 | UC-01, UC-02, UC-03 · FR-01-01, FR-01-02, FR-01-03 | Must | `screens/screen-spec-S-F01-02.md` |
| S-F01-03 | Create or edit resident profile | Receptionist | F-01 | Receptionist enters a new resident's details, or edits the information of an existing resident, and saves | S-F01-01 (create); S-F01-02 (edit) | UC-01, UC-02 · FR-01-01, FR-01-02 | Must | `screens/screen-spec-S-F01-03.md` |
| S-F02-01 | Condition history timeline | Nurse | F-02 | Nurse reads all condition records of the resident in time order | S-F01-01; S-F01-02 | UC-04 · FR-02-04 | Should | `screens/screen-spec-S-F02-01.md` |
| S-F02-02 | Update resident condition | Nurse | F-02 | Nurse enters the resident's condition and saves it; the resident's name and room are shown | S-F01-01; S-F01-02; S-F02-01 | UC-05 · FR-02-01, FR-02-02 | Must | `screens/screen-spec-S-F02-02.md` |
| S-F03-01 | Alert list | Nurse | F-03 | Nurse sees the open alerts (New, Escalated, Acknowledged), newest first; acknowledges one, or reports the outcome after handling it | Menu; real-time alert pop-up; push notice on the phone | UC-06 · FR-03-02, FR-03-03, FR-03-06 | Must | `screens/screen-spec-S-F03-01.md` |
| S-F03-02 | Alert notice and closed notice with outcome | Residents' Family | F-03 | Family member reads the notice of a dangerous alert for their resident and, later, the closed notice with the outcome | Push notice on the phone; menu | UC-07 · FR-03-09, FR-03-10 | Must | `screens/screen-spec-S-F03-02.md` |
| S-F04-01 | Nurse chat with family | Nurse | F-04 | Nurse reads the conversation with a resident's family and sends a message | S-F01-02; S-F02-01; menu | UC-09 · FR-04-03, FR-04-04 | Should | `screens/screen-spec-S-F04-01.md` |
| S-F04-02 | Resident condition | Residents' Family | F-04 | Home of the family member: selects one of their linked residents and reads the current condition | Home after sign in (family); menu | UC-08 · FR-04-01, FR-04-02 | Must | `screens/screen-spec-S-F04-02.md` |
| S-F04-03 | Chat with nurse | Residents' Family | F-04 | Family member reads the conversation with the nurses and sends a message | S-F04-02; menu | UC-09 · FR-04-03, FR-04-04 | Should | `screens/screen-spec-S-F04-03.md` |
| S-F05-01 | Report with period filter | BOD | F-05 | Home of the BOD: selects a period and sees the number of alerts, the average time from alert to acknowledgement and the number of escalated alerts | Home after sign in (BOD) | UC-10 · FR-05-01 | Could | `screens/screen-spec-S-F05-01.md` |
| S-F06-01 | Account management | Admin | F-06 | Home of the Admin: chooses an operation (create, edit, deactivate, assign role) and selects an Active account | Home after sign in (admin); menu | UC-11, UC-12 · FR-06-01, FR-06-02, FR-06-03, FR-06-06, FR-06-07 | Must | `screens/screen-spec-S-F06-01.md` |
| S-F06-02 | Create or edit account | Admin | F-06 | Admin enters a new account (with the residents to link for a family account), or changes the name, contact information or password of an Active account | S-F06-01 (create, edit) | UC-11 · FR-06-01, FR-06-04, FR-06-05, FR-06-06 | Must | `screens/screen-spec-S-F06-02.md` |
| S-F06-03 | Assign role to account | Admin | F-06 | Admin sees the account's current role and the roles they may assign, selects one and confirms | S-F06-01 | UC-12 · FR-06-02, FR-06-03 | Must | `screens/screen-spec-S-F06-03.md` |
| S-F06-04 | Radar status list | Admin | F-06 | Admin sees every registered radar with its status (Online or Offline) and the time of its last data; Offline radars are highlighted | Menu | UC-13 · FR-06-03, FR-06-09, FR-06-10, FR-06-11 | Could | `screens/screen-spec-S-F06-04.md` |
| S-F06-05 | Log list with filters | Admin | F-06 | Admin lists access and action entries, filtered by user and date | Menu; S-F06-04 | UC-14 · FR-06-12 | Could | `screens/screen-spec-S-F06-05.md` |

## Modals and overlays

| Modal | Opened from screen | Purpose |
|---|---|---|
| Real-time alert pop-up | Any signed-in nurse screen: S-F01-01, S-F01-02, S-F02-01, S-F02-02, S-F03-01, S-F04-01 (opened by the system) | Shows a new or repeated alert, Notify nurses, count 1 and Notify nurses again, add 1 to count (`AD-UC-06` s2, s5): the resident, location and time, with sound (FR-03-02); "Acknowledge" (FR-03-03, FR-03-04) |
| Close alert with outcome | S-F03-01 | Handle and report outcome, then Close alert (`AD-UC-06` n2, s6); the outcome is a real event or a false alarm (FR-03-06) |
| Deactivate account | S-F06-01, after Select an active account to deactivate (`AD-UC-11` A5) | Does Admin confirm the deactivation? (`AD-UC-11` D6, FR-06-07): [Yes] goes on to Deactivate account (S8); [No] ends the flow and nothing is deactivated |

## What is deliberately not a screen

| Candidate | Why it is not a screen |
|---|---|
| Display the validation errors (`AD-UC-01` errors, `AD-UC-02` s3) | Error state of S-F01-03; the flow returns to editing (FR-01-01, FR-01-02) |
| See the profile created with its resident ID (`AD-UC-01` view) | Success of S-F01-03 after create, shown on S-F01-02 (FR-01-01) |
| See the updated information (`AD-UC-02` a4) | Success of S-F01-03 after edit, shown on S-F01-02 (FR-01-02) |
| Reject the save, leave earlier records unchanged, tell the nurse why and let the nurse correct the condition (`AD-UC-05` s0) | Error state of S-F02-02; the nurse does Correct condition on the same screen (`AD-UC-05` a3, FR-02-01) |
| Keep it as a new record, earlier records unchanged (`AD-UC-05` s2) | No user interface; the records are listed on S-F02-01 (FR-02-03) |
| Receive radar event; Store alert (`AD-UC-06` s0, s1) | Started by the Radar, no user interface (FR-03-01) |
| Notify nurses, count 1; Notify nurses again, add 1 to count (`AD-UC-06` s2, s5) | Shown to the nurse as the real-time alert pop-up; s5 runs after the time event 1 min after notice (`AD-UC-06` t1, FR-03-02, FR-03-04) |
| Record acknowledgement (`AD-UC-06` s4) | Success of "Acknowledge" on the real-time alert pop-up or S-F03-01: Alert [Acknowledged] (FR-03-03) |
| Close alert (`AD-UC-06` s6) | Success of the Close alert with outcome modal: Alert [Closed] (FR-03-06) |
| Log failed alert (`AD-UC-06` s10) | Runs after the time event 1 min after notice when 3 notices were sent, no user interface (`AD-UC-06` t1, d3, FR-03-05) |
| Send first notice to family, once per alert; Send closed notice to family, once (`AD-UC-07` s3, s9) | Sent by the system; the family does Receive first notice and Receive closed notice on S-F03-02 (`AD-UC-07` fam1, fam2, FR-03-09, FR-03-10) |
| Skip push; Retry push, add 1 to retry count; Log failed push (`AD-UC-07` s11, s13, s12) | No user interface (FR-03-11) |
| Deny the request with an access-denied message (`AD-UC-08` s2) | Error state of S-F04-02 (FR-04-01) |
| Tell the family member that no condition has been recorded yet (`AD-UC-08` s3) | State of S-F04-02 when no condition is recorded (`AD-UC-08` d2 [none recorded], FR-04-02) |
| Add message to conversation with the nurses; Add message to conversation with that family (`AD-UC-09` s1, s2) | Success state of S-F04-03 and S-F04-01 (FR-04-03, FR-04-04) |
| Reject request (`AD-UC-11` S1); Reject request due to insufficient permission (`AD-UC-12` S1, `AD-UC-13` S1) | Refused request: the user returns to the home screen of their role with an access-denied message (FR-06-03) |
| Identify and report information errors (`AD-UC-11` S2) | Error state of S-F06-02; the Admin answers Continue creating or editing the account? on the same screen (`AD-UC-11` D3, FR-06-04) |
| Deactivate account (`AD-UC-11` S8) | Success of the Deactivate account modal when D6 is [Yes]: Account [Inactive] (`AD-UC-11` D6, O2, FR-06-07) |
| Confirm that the operation is complete (`AD-UC-11` S4) | Success state of S-F06-02 after create or edit, and of S-F06-01 after deactivate (FR-06-01, FR-06-06, FR-06-07) |
| Record the action in the action log (`AD-UC-11` S6, `AD-UC-12` S7) | No user interface; the entries are listed on S-F06-05 (FR-06-08) |
| Reject role assignment request outside the permitted scope (`AD-UC-12` S5) | Error state of S-F06-03; the Admin chooses again (FR-06-03) |
| Tell Admin that the radar status cannot be shown (`AD-UC-13` S4) | Error state of S-F06-04 (FR-06-11) |
