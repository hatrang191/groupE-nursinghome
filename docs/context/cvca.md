# CVCA: Dien Hong

The Customer Value Chain Analysis lists every stakeholder of the Dien Hong Nursing House and
what flows between them: money (`$`), information (`!`) and goods or services. It is drawn before
the system is designed, to find the problems and opportunities.

| Field | Value |
|---|---|
| Version | v1.1 |
| Next | [Context Diagram](context-diagram.md) |

## 1. Value chain (with the new system)

The dashed box is the Dien Hong Nursing House. Everything outside it is an outside stakeholder.

```mermaid
flowchart LR
  MOH["Ministry of Health"]
  FAM["Residents' Family"]
  SUP["Medical suppliers<br>and equipment"]
  EXT["External Service Providers<br>(Traditional Medicine, ...)"]

  subgraph NH["Dien Hong Nursing House"]
    BOD["Dien Hong Nursing House - BOD"]
    REC["Receptionist<br>(also Accountant)"]
    ADM["Admin"]
    DH(["Dien Hong system"])
    NUR["Nurse"]
    RES["Residents"]
    RAD["Radar"]
  end

  MOH -->|"policy"| BOD
  SUP -->|"service"| BOD
  BOD -->|"$"| SUP
  BOD -->|"$"| EXT
  FAM -->|"$"| BOD
  FAM -->|"!"| REC
  RES -->|"!"| REC
  REC -->|"!"| DH
  ADM -->|"!"| DH
  RAD -->|"!"| DH
  DH -->|"!"| BOD
  DH -->|"!"| NUR
  NUR -->|"!"| DH
  DH -->|"!"| FAM
  FAM -->|"!"| DH
  NUR -->|"care service"| RES
  EXT -->|"service"| RES
```

## Legend

| Symbol | Meaning |
|---|---|
| `!` | Resident information, condition updates, alerts, messages, reports, accounts |
| `$` | Money |
| Service | Care service, policy |

## Change log

| Version | Change | Why |
|---|---|---|
| v1.0 | First version | |
| v1.1 | Added the flows between the system and the Residents' Family (alerts and condition to the family, messages from the family); added Admin; renamed the Receptionist label; changed the legend | The family is the main receiver of the new system's information, and the Context Diagram needs the Admin |
