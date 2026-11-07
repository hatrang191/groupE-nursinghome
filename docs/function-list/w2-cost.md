# W2 Costing and roll-up: Dien Hong System

Lesson 5 (block W2). Fully derived from [W1](w1-function-list.md), the effective priorities in [W4](w4-priorities.md) and the class Parameters:

- Person-hours = base hours x AI leverage factor + base hours x verification factor.
- AI leverage: FE 0.35, BE 0.7, INT 1.0. Verification factor: 0.15 when the EARS criterion is precise, 0.4 when it is vague.
- Rate: FE Junior 200.000 VND/h; BE and INT Senior 300.000 VND/h.

## Cost per L3 work item

| L3 ID | Feature | Layer | Base h | AI-adj. h | Verif. h | Person-hours | Band | Cost (VND) | Effective priority |
|---|---|---|---|---|---|---|---|---|---|
| FR-00-01-FE | F-00 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-00-01-BE | F-00 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-01-01-FE | F-01 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-01-01-BE | F-01 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-01-02-FE | F-01 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-01-02-BE | F-01 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-01-03-FE | F-01 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-01-03-BE | F-01 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-02-01-BE | F-02 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-02-02-FE | F-02 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-02-02-BE | F-02 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-02-03-BE | F-02 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-02-04-FE | F-02 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-02-04-BE | F-02 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-03-01-INT | F-03 | INT | 16 | 16.0 | 2.4 | 18.4 | Senior | 5.520.000 | Must |
| FR-03-01-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-03-02-FE | F-03 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-03-02-BE | F-03 | BE | 8 | 5.6 | 1.2 | 6.8 | Senior | 2.040.000 | Must |
| FR-03-03-FE | F-03 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-03-03-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-03-04-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-03-05-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-03-06-FE | F-03 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-03-06-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-03-07-BE | F-03 | BE | 8 | 5.6 | 1.2 | 6.8 | Senior | 2.040.000 | Should |
| FR-03-08-INT | F-03 | INT | 8 | 8.0 | 1.2 | 9.2 | Senior | 2.760.000 | Must |
| FR-03-09-FE | F-03 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-03-09-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-03-09-INT | F-03 | INT | 8 | 8.0 | 3.2 | 11.2 | Senior | 3.360.000 | Must |
| FR-03-10-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-03-10-INT | F-03 | INT | 4 | 4.0 | 0.6 | 4.6 | Senior | 1.380.000 | Could |
| FR-03-11-BE | F-03 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-04-01-FE | F-04 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-04-01-BE | F-04 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-04-02-FE | F-04 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-04-02-BE | F-04 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-04-03-FE | F-04 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-04-03-BE | F-04 | BE | 8 | 5.6 | 1.2 | 6.8 | Senior | 2.040.000 | Should |
| FR-04-04-FE | F-04 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-04-04-BE | F-04 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-05-01-FE | F-05 | FE | 8 | 2.8 | 1.2 | 4.0 | Junior | 800.000 | Could |
| FR-05-01-BE | F-05 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-06-01-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-06-01-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-06-02-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-06-02-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-06-03-BE | F-06 | BE | 16 | 11.2 | 2.4 | 13.6 | Senior | 4.080.000 | Must |
| FR-06-04-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-06-04-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-06-05-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-06-05-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-06-06-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Should |
| FR-06-06-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-06-07-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Must |
| FR-06-07-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Must |
| FR-06-08-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |
| FR-06-09-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Could |
| FR-06-09-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-06-09-INT | F-06 | INT | 4 | 4.0 | 0.6 | 4.6 | Senior | 1.380.000 | Could |
| FR-06-10-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Could |
| FR-06-11-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Could |
| FR-06-12-FE | F-06 | FE | 4 | 1.4 | 0.6 | 2.0 | Junior | 400.000 | Could |
| FR-06-12-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Could |
| FR-06-13-BE | F-06 | BE | 4 | 2.8 | 0.6 | 3.4 | Senior | 1.020.000 | Should |

## Roll-up per feature

| Feature | Name | L2 | L3 | Base h | Must p-h | Should p-h | Could p-h | In-scope p-h | In-scope cost (VND) | Must share | Transactional FP | Base h per FP |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| F-00 | Platform and Access | 1 | 2 | 8 | 5.4 | 0.0 | 0.0 | 5.4 | 1.420.000 | 100% | 4 | 2.0 |
| F-01 | Resident Profile Management | 3 | 6 | 24 | 5.4 | 10.8 | 0.0 | 16.2 | 4.260.000 | 33% | 12 | 2.0 |
| F-02 | Condition Tracking | 4 | 6 | 24 | 12.2 | 5.4 | 0.0 | 17.6 | 4.880.000 | 69% | 8 | 3.0 |
| F-03 | Abnormal Alert Handling | 11 | 18 | 100 | 70.6 | 6.8 | 14.8 | 92.2 | 26.860.000 | 77% | 27 | 3.7 |
| F-04 | Family Communication | 4 | 8 | 36 | 10.8 | 14.2 | 0.0 | 25.0 | 6.700.000 | 43% | 12 | 3.0 |
| F-05 | Dashboard & Reporting | 1 | 2 | 12 | 0.0 | 0.0 | 7.4 | 7.4 | 1.820.000 | 0% | 5 | 2.4 |
| F-06 | System Administration | 13 | 22 | 100 | 35.2 | 17.6 | 19.4 | 72.2 | 19.660.000 | 49% | 28 | 3.6 |
| **Project** | | **37** | **64** | **304** | **139.6** | **54.8** | **41.6** | **236.0** | **65.600.000** | **59%** | **96** | **3.2** |

## The three headline figures

| Scenario | Scope | Person-hours | Cost (VND) | Meaning |
|---|---|---|---|---|
| Committed | Must only | 139.6 | 39.280.000 | What the team guarantees to deliver |
| Expected | Must + Should | 194.4 | 54.320.000 | The figure given to the client; the base cash flow in W6 |
| Ceiling | Must + Should + Could | 236.0 | 65.600.000 | If the Could pool is never consumed |

## Threshold check (after W4)

| Indicator | Value | Threshold | Verdict |
|---|---|---|---|
| Must share of in-scope effort | 59.2% | at most 60% | PASS |
| Could share (contingency pool) | 17.6% | target 20% | PASS |
| In-scope effort vs team capacity | 236.0 p-h | at most 240 p-h | PASS |
| Hidden technical work, share of effort | 15.4% | at least 10% | PASS |
| L3 rows above 16 hours | 0 | 0 | PASS |
| Feature count | 7 | 3 to 6 | REVIEW (F-00 is the platform feature) |
| Function-point cross-check: 96 transactional FP x 3 h = 288 h vs 304 bottom-up base h | 5.3% apart | at most 30% | PASS |

Three observations the team carries into W4:

1. Must sits at 59% of the effort, close to the 60% line; any new Must needs a Must to move down. In-scope effort leaves only 4.0 p-h of the 240 p-h capacity.
2. Abnormal Alert Handling (F-03) carries 39% of the in-scope effort and all three Must integrations (FR-03-01-INT, FR-03-08-INT, FR-03-09-INT); it is the main delivery risk and starts first in DBIZ3.
3. Families can see their relative's condition from day one: FR-04-01 and FR-04-02 are Must. To keep Must under 60%, viewing the full resident profile (FR-01-03) moved to Should (nurses still see the resident's name and room on the condition and alert screens; the full profile page is important but not release-blocking), so F-01 keeps one Must row (FR-01-01).
