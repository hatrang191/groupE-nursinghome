# W4 Priorities: Dien Hong System

Lesson 5 (block W4). Cut by lowering priority, never by lowering estimates. Every change names its rule.

| Rule | MoSCoW meaning | Test question |
|---|---|---|
| R1 Must | The release has no viable use without it | If it is absent on delivery day, is the release useless? |
| R2 Should | Important, but a workaround exists | Painful to leave out, yet the solution still works? |
| R3 Could | Desirable, low impact if dropped | Would anyone notice within the first month? |
| R4 Won't | Agreed out of scope for this release | Did the client accept deferring it? |

| Feature | L2 ID | L2 function name | Initial priority | Effective priority | Justification (required if changed) | Person-hours | Cost (VND) |
|---|---|---|---|---|---|---|---|
| F-00 | FR-00-01 | Sign in | Must | Must |  | 5.4 | 1.420.000 |
| F-01 | FR-01-01 | Create new resident profile | Must | Must |  | 5.4 | 1.420.000 |
| F-01 | FR-01-02 | Update resident information | Should | Should |  | 5.4 | 1.420.000 |
| F-01 | FR-01-03 | View resident's profile | Must | Should | R2: A workaround exists: nurses still see the resident's name and room on the condition and alert screens; the full profile page is important but not release-blocking. | 5.4 | 1.420.000 |
| F-02 | FR-02-01 | Validate condition | Must | Must |  | 3.4 | 1.020.000 |
| F-02 | FR-02-02 | Update condition | Must | Must |  | 5.4 | 1.420.000 |
| F-02 | FR-02-03 | Keep condition history (non-functional) | Must | Must |  | 3.4 | 1.020.000 |
| F-02 | FR-02-04 | View condition history | Should | Should |  | 5.4 | 1.420.000 |
| F-03 | FR-03-01 | Receive abnormal alert from radar | Must | Must |  | 21.8 | 6.540.000 |
| F-03 | FR-03-02 | Receive alert (nurse) | Must | Must |  | 8.8 | 2.440.000 |
| F-03 | FR-03-03 | Acknowledge alert | Must | Must |  | 5.4 | 1.420.000 |
| F-03 | FR-03-04 | Notify nurses again when nobody acknowledges | Must | Must |  | 3.4 | 1.020.000 |
| F-03 | FR-03-05 | Log failed alert | Must | Could | R3: If nobody responds after 3 notices, the alert still stays on every nurse's screen as Escalated. Logging the failure is useful for checking later, but the system works without it. | 3.4 | 1.020.000 |
| F-03 | FR-03-06 | Close alert with outcome | Must | Must |  | 5.4 | 1.420.000 |
| F-03 | FR-03-07 | Group repeated radar events | Should | Should |  | 6.8 | 2.040.000 |
| F-03 | FR-03-08 | Always on (non-functional) | Must | Must |  | 9.2 | 2.760.000 |
| F-03 | FR-03-09 | Receive alert notice (family) | Should | Must | R1: Telling families quickly when something dangerous happens is a main purpose of Dien Hong. Without it, families only find out when they open the app or someone calls them. | 16.6 | 4.780.000 |
| F-03 | FR-03-10 | Send closed notice to family | Could | Could |  | 8.0 | 2.400.000 |
| F-03 | FR-03-11 | Skip or retry family push | Should | Could | R3: The family still gets the first notice without this. Skipping or retrying the push only makes it more reliable, so it can be dropped if time runs out. | 3.4 | 1.020.000 |
| F-04 | FR-04-01 | Verify family access to resident (non-functional) | Must | Must |  | 5.4 | 1.420.000 |
| F-04 | FR-04-02 | View resident's condition | Should | Must | R1: Seeing the resident's current condition is the main daily reason families use the app; a phone call to the nursing home cannot replace it for every family. | 5.4 | 1.420.000 |
| F-04 | FR-04-03 | Chat with nurse | Should | Should |  | 8.8 | 2.440.000 |
| F-04 | FR-04-04 | Chat with family | Should | Should |  | 5.4 | 1.420.000 |
| F-05 | FR-05-01 | View report | Could | Could |  | 7.4 | 1.820.000 |
| F-06 | FR-06-01 | Set up account | Must | Must |  | 5.4 | 1.420.000 |
| F-06 | FR-06-02 | Assign role-based permissions | Must | Must |  | 5.4 | 1.420.000 |
| F-06 | FR-06-03 | Enforce role-based access (non-functional) | Must | Must |  | 13.6 | 4.080.000 |
| F-06 | FR-06-04 | Validate and correct account information | Must | Should | R2: Only a trained Admin creates accounts, so mistakes are rare. If one happens, the Admin can edit or deactivate the account. | 5.4 | 1.420.000 |
| F-06 | FR-06-05 | Link family account to resident | Must | Must |  | 5.4 | 1.420.000 |
| F-06 | FR-06-06 | Edit account information | Should | Should |  | 5.4 | 1.420.000 |
| F-06 | FR-06-07 | Deactivate account | Must | Must |  | 5.4 | 1.420.000 |
| F-06 | FR-06-08 | Record account and role changes in the action log (non-functional) | Should | Should |  | 3.4 | 1.020.000 |
| F-06 | FR-06-09 | Show radar status | Must | Could | R3: Alerts still work without this page, and staff can check a radar in the room. The page only helps the Admin find a broken radar faster. | 10.0 | 2.800.000 |
| F-06 | FR-06-10 | Highlight Offline radars | Could | Could |  | 2.0 | 400.000 |
| F-06 | FR-06-11 | Report radar status unavailable | Could | Could |  | 2.0 | 400.000 |
| F-06 | FR-06-12 | View access and action logs | Could | Could |  | 5.4 | 1.420.000 |
| F-06 | FR-06-13 | Record access and action logs (non-functional) | Should | Should |  | 3.4 | 1.020.000 |
