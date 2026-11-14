# W5 Baseline and handover: Dien Hong System

Lesson 5 (block W5). The baseline every later lesson and DBIZ3 refer to.

## 1. Three scenarios

| Scenario | Person-hours | Cost (VND) | Role in the cash-flow analysis (W6) |
|---|---|---|---|
| Pessimistic: Must only | 139.6 | 39.280.000 | Lowest investment, lowest benefit |
| Base: Must + Should | 197.8 | 55.340.000 | The client figure; base cash flow |
| Optimistic: full scope | 236.0 | 65.600.000 | If the Could pool is never consumed |

## 2. Assumptions

| Assumption | Value | Source |
|---|---|---|
| Junior / Senior / Expert rate | 200.000 / 300.000 / 400.000 VND per hour | Class Parameters (ITviec 2025-26 salary report) |
| AI leverage FE / BE / INT | 0.35 / 0.7 / 1.0 | Class Parameters |
| Verification factor precise / vague | 0.15 / 0.4 | Class Parameters |
| Hours per function point (cross-check only) | 3 | Class Parameters |
| Team capacity | 240 person-hours (4 people x 12 weeks x 5 h) | Class Parameters |
| Money contingency on the Must set | 10% = 3.928.000 VND | Class Parameters |

## 3. tasks.md seed (effective priority Must)

Risk order: 1 = INT (external services), 2 = BE, 3 = FE. DBIZ3 starts with the riskiest rows.

| L3 ID | Layer | Person-hours | Risk order |
|---|---|---|---|
| FR-03-01-INT | INT | 18.4 | 1 |
| FR-03-08-INT | INT | 9.2 | 1 |
| FR-03-09-INT | INT | 11.2 | 1 |
| FR-00-01-BE | BE | 3.4 | 2 |
| FR-01-01-BE | BE | 3.4 | 2 |
| FR-02-01-BE | BE | 3.4 | 2 |
| FR-02-02-BE | BE | 3.4 | 2 |
| FR-02-03-BE | BE | 3.4 | 2 |
| FR-03-01-BE | BE | 3.4 | 2 |
| FR-03-02-BE | BE | 6.8 | 2 |
| FR-03-03-BE | BE | 3.4 | 2 |
| FR-03-04-BE | BE | 3.4 | 2 |
| FR-03-06-BE | BE | 3.4 | 2 |
| FR-03-09-BE | BE | 3.4 | 2 |
| FR-04-01-BE | BE | 3.4 | 2 |
| FR-04-02-BE | BE | 3.4 | 2 |
| FR-06-01-BE | BE | 3.4 | 2 |
| FR-06-02-BE | BE | 3.4 | 2 |
| FR-06-03-BE | BE | 13.6 | 2 |
| FR-06-05-BE | BE | 3.4 | 2 |
| FR-06-07-BE | BE | 3.4 | 2 |
| FR-00-01-FE | FE | 2.0 | 3 |
| FR-01-01-FE | FE | 2.0 | 3 |
| FR-02-02-FE | FE | 2.0 | 3 |
| FR-03-02-FE | FE | 2.0 | 3 |
| FR-03-03-FE | FE | 2.0 | 3 |
| FR-03-06-FE | FE | 2.0 | 3 |
| FR-03-09-FE | FE | 2.0 | 3 |
| FR-04-01-FE | FE | 2.0 | 3 |
| FR-04-02-FE | FE | 2.0 | 3 |
| FR-06-01-FE | FE | 2.0 | 3 |
| FR-06-02-FE | FE | 2.0 | 3 |
| FR-06-05-FE | FE | 2.0 | 3 |
| FR-06-07-FE | FE | 2.0 | 3 |

## 4. Open questions for the client

Collected from the activity diagrams, the ADRs and the business rules. Defaults are what the team builds until the client answers.

| # | Question | Blocking? | Owning feature | Default applied |
|---|---|---|---|---|
| 1 | Which fields of a resident profile are required, and what makes a detail invalid? (AD-UC-01 Q1, BR-01-02) | Yes (FR-01-01, FR-01-02) | F-01 | Full name left empty is the invalid case |
| 2 | Is the 1-minute resend the same for every severity? (AD-UC-06 Q1) | Yes (FR-03-04) | F-03 | 1 minute for all |
| 3 | Does "3 notices" include the first notice? (AD-UC-06 Q3) | Yes (FR-03-04, FR-03-05) | F-03 | Yes: the first notice and two more |
| 4 | After the failure is logged, what happens to the alert, and is anybody told? (AD-UC-06 Q2) | Yes (FR-03-05) | F-03 | The alert stays open on the nurses' list |
| 5 | If the system is down for a moment, does the radar keep its events and send them later? (AD-UC-06 Q7) | Yes (FR-03-01, FR-03-08) | F-03 | The radar keeps events and resends |
| 6 | Which severity counts as dangerous? The thresholds are set when the radar's AI is trained. (AD-UC-07 Q1, BR-03-01) | Yes (FR-03-09) | F-03 | Severity High only |
| 7 | How long does the system wait between two retries of a failed family push? (AD-UC-07 Q5, BR-03-05) | Yes (FR-03-11) | F-03 | 1 minute |
| 8 | Must a nurse be assigned to a resident before exchanging messages with the family? (AD-UC-09 Q2) | Yes (FR-04-03, FR-04-04) | F-04 | Any nurse and any family member can exchange messages |
| 9 | After how long without a successful connection check is a radar Offline? (AD-UC-13 Q1, ADR-002) | No | F-06 | 5 minutes |
| 10 | How often does the Radar Gateway check each radar's connection? (ADR-002) | No | F-06 | Not decided; must be shorter than the Offline limit |
| 11 | Does the RETINA-4SN answer a status request, or can the gateway only check that it is reachable on the network? (ADR-002) | No | F-06 | Network reachability check |
| 12 | Should a family member get a push notice for a new chat message when the app is closed? (AD-UC-09) | No | F-04 | No push; messages show when the app is opened |
