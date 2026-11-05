# CVCA: Dien Hong

The Customer Value Chain Analysis lists every stakeholder of the Dien Hong Nursing House and
what flows between them: money (`$`), information (`!`) and goods or services. It is drawn before
the system is designed, to find the problems and opportunities.

| Field | Value |
|---|---|
| Version | v1.0 |
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
    REC["Receptionist - Accountant"]
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
  RAD -->|"!"| DH
  DH -->|"!"| BOD
  DH -->|"!"| NUR
  NUR -->|"!"| DH
  NUR -->|"care service"| RES
  EXT -->|"service"| RES
```

## Legend

| Symbol | Meaning |
|---|---|
| `!` | Resident information, care updates, task status, request, bookings |
| `$` | Money |
| Service | Care service, policy |
