# Screen Transition Diagram: Dien Hong System

## 1. Receptionist

```mermaid
flowchart LR
  entry(["Opens Dien Hong System"])
  S00_01["S-F00-01<br>Sign in"]
  S01_01["S-F01-01<br>Resident list"]
  S01_02["S-F01-02<br>Resident profile detail"]
  S01_03["S-F01-03<br>Create or edit resident profile"]
  entry --> S00_01
  S00_01 -->|"[signed in as receptionist]"| S01_01
  S01_01 -->|"taps a resident"| S01_02
  S01_01 -->|"taps New resident"| S01_03
  S01_02 -->|"taps Edit"| S01_03
  S01_02 -->|"taps Back"| S01_01
  S01_03 -->|"[resident saved]"| S01_02
  S01_03 -->|"taps Cancel"| S01_01
```



## 2. Nurse

```mermaid
flowchart LR
  entry(["Opens Dien Hong System"])
  alert(["Taps a push notice or the real-time alert pop-up"])
  S00_01["S-F00-01<br>Sign in"]
  S01_01["S-F01-01<br>Resident list"]
  S01_02["S-F01-02<br>Resident profile detail"]
  S02_01["S-F02-01<br>Condition history timeline"]
  S02_02["S-F02-02<br>Update resident condition"]
  S03_01["S-F03-01<br>Alert list"]
  S04_01["S-F04-01<br>Nurse chat with family"]
  entry --> S00_01
  S00_01 -->|"[signed in as nurse]"| S01_01
  alert --> S03_01
  S01_01 -->|"taps a resident"| S01_02
  S01_01 -->|"taps Update condition on a resident"| S02_02
  S01_01 -->|"taps Alerts in the menu"| S03_01
  S01_02 -->|"taps Condition history"| S02_01
  S01_02 -->|"taps Update condition"| S02_02
  S01_02 -->|"taps Message family"| S04_01
  S01_02 -->|"taps Back"| S01_01
  S02_01 -->|"taps Update condition"| S02_02
  S02_01 -->|"taps Message family"| S04_01
  S02_01 -->|"taps Back"| S01_02
  S02_02 -->|"[condition saved]"| S02_01
  S02_02 -->|"taps Cancel"| S01_01
  S03_01 -->|"taps Close alert, reports the outcome in dialog"| S03_01
  S03_01 -->|"taps Back"| S01_01
  S04_01 -->|"taps Back"| S01_02
```

## 3. Residents' Family

```mermaid
flowchart LR
  entry(["Opens Dien Hong System"])
  push(["Taps a push notice on the phone"])
  S00_01["S-F00-01<br>Sign in"]
  S03_02["S-F03-02<br>Alert notice and closed notice with outcome"]
  S04_02["S-F04-02<br>Resident condition"]
  S04_03["S-F04-03<br>Chat with nurse"]
  entry --> S00_01
  S00_01 -->|"[signed in as family member]"| S04_02
  push --> |"sees push notice"| S03_02
  S04_02 -->|"taps Chat with nurse"| S04_03
  S04_02 -->|"taps Alerts in the menu"| S03_02
  S04_03 -->|"taps Back"| S04_02
  S03_02 -->|"taps Back"| S04_02
```

## 4. BOD

```mermaid
flowchart LR
  entry(["Open Dien Hong System"])
  S00_01["S-F00-01<br>Sign in"]
  S05_01["S-F05-01<br>Report with period filter"]
  entry --> S00_01
  S00_01 -->|"[signed in as BOD]"| S05_01
```


## 5. Admin

```mermaid
flowchart LR
  entry(["Opens Dien Hong System"])
  S00_01["S-F00-01<br>Sign in"]
  S06_01["S-F06-01<br>Account management"]
  S06_02["S-F06-02<br>Create or edit account"]
  S06_03["S-F06-03<br>Assign role to account"]
  S06_04["S-F06-04<br>Radar status list"]
  S06_05["S-F06-05<br>Log list with filters"]
  entry --> S00_01
  S00_01 -->|"[signed in as admin]"| S06_01
  S06_01 -->|"taps Create account or Edit account"| S06_02
  S06_01 -->|"taps Assign role"| S06_03
  S06_01 -->|"taps Deactivate, confirms in dialog"| S06_01
  S06_01 -->|"taps Radar status in the menu"| S06_04
  S06_01 -->|"taps Logs in the menu"| S06_05
  S06_02 -->|"[account saved] taps Back"| S06_01
  S06_02 -->|"taps Cancel"| S06_01
  S06_03 -->|"[role assigned] taps Back"| S06_01
  S06_03 -->|"taps Cancel"| S06_01
  S06_04 -->|"taps View logs"| S06_05
  S06_04 -->|"taps Account management in the menu"| S06_01
  S06_05 -->|"taps Account management in the menu"| S06_01
  S06_05 -->|"taps Radar status in the menu"| S06_04
```


## Checks

- Every screen in the Screen List appears: yes (17 of 17).
- Every screen reachable from an entry point: yes.
- No dead ends: yes.
- Usage-flow paths: every "UC-a to UC-b" arrow of [usage-flow.md](usage-flow.md) has a path.
- Sign in is shared.
- A refused request (access denied) sends the user back to the home screen of their role with a message (FR-06-03). 

## Usage-flow paths

| Actor             | Usage-flow arrow | Path on this diagram                                                                                                                                                                     |
| ----------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Nurse             | UC-03 to UC-04   | S-F01-02 Resident profile detail → S-F02-01 Condition history timeline ("taps Condition history")                                                                                        |
| Nurse             | UC-04 to UC-05   | S-F02-01 Condition history timeline → S-F02-02 Update resident condition ("taps Update condition")                                                                                       |
| Nurse             | UC-04 to UC-09   | S-F02-01 Condition history timeline → S-F04-01 Nurse chat with family ("taps Message family")                                                                                            |
| Residents' Family | UC-08 to UC-09   | S-F04-02 Resident condition → S-F04-03 Chat with nurse ("taps Chat with nurse")                                                                                                          |
| Admin             | UC-11 to UC-12   | S-F06-02 Create or edit account → S-F06-01 Account management ("[account saved] taps Back") → S-F06-03 Assign role to account ("taps Assign role"); S-F06-01 serves both UC-11 and UC-12 |
| Admin             | UC-14 to UC-12   | S-F06-05 Log list with filters → S-F06-01 Account management ("taps Account management in the menu") → S-F06-03 Assign role to account ("taps Assign role")                              |
| Admin             | UC-13 to UC-14   | S-F06-04 Radar status list → S-F06-05 Log list with filters ("taps View logs")                                                                                                           |


