# Sequence Diagram: SD-UC-09 Exchange messages between family and nurses

Analysis level: how the parts of the Dien Hong System talk to each other when a family member and
a nurse exchange messages. The lifelines are the elements of the
[physical design](../architecture/architecture.md). Whether the conversation continues after a
message is shown in the activity diagram [AD-UC-09](../activity/AD-UC-09.md), not here.

| Field | Value |
|---|---|
| Use case | UC-09 Exchange messages between family and nurses |
| Version | v1 |
| FRs | FR-04-03, FR-04-04 |

```mermaid
sequenceDiagram
  actor FAM as Residents' Family
  participant W as Web App
  participant API as API Server
  participant DB as Database
  actor NUR as Nurse

  alt Family member sends a message
    FAM->>W: Write a message and send it on Chat with nurse (S-F04-03)
    W->>API: sendFamilyMessage(residentId, text)
    API->>API: Check session token and role permission
    API->>DB: Insert message into the conversation with the nurses (FR-04-03)
    DB-->>API: Message stored
    API-->>W: Message stored, deliver to the nurse on duty (FR-04-03)
    W-->>NUR: Show the new message on Nurse chat with family (S-F04-01)
    W-->>FAM: Show the sent message in the conversation
  else Nurse sends a message
    NUR->>W: Write a message and send it on Nurse chat with family (S-F04-01)
    W->>API: sendNurseMessage(residentId, text)
    API->>API: Check session token and role permission
    API->>DB: Insert message into the conversation with that family (FR-04-04)
    DB-->>API: Message stored
    API-->>W: Message stored, deliver to the family account (FR-04-04)
    W-->>FAM: Show the new message on Chat with nurse (S-F04-03)
    W-->>NUR: Show the sent message in the conversation
  end
```

The two branches are the two directions of the use case. In AD-UC-09 they are the decision `d1`
"Who initiates the message?" (`a1` then `s1` for FR-04-03, `a2` then `s2` for FR-04-04). Who writes
first is the user's choice, so the `alt` here only separates the two directions and is not a
decision made by the system. Sending another message is the loop `d2` in AD-UC-09 and repeats the
same exchange, so it is not drawn as a second fragment. Neither side has to reply (AD-UC-09 open
question 1). The defaults of AD-UC-09 are applied. No nurse-to-resident or nurse-to-family link
is checked, so any family member and any nurse can exchange messages (open question 2), and no
failure flow is drawn: if the message cannot be stored, it is not added (open question 3). The
message goes to "the nurse on duty" as FR-04-03 states, and the diagram shows only that the
system stores and delivers it.
