# **Activity Diagram: AD-UC-09 Exchange messages between family and nurses**

| Field | Value |
| :---- | :---- |
| AD ID | AD-UC-09 |
| Use case | UC-09 Exchange messages between family and nurses |
| Feature | F-04 Family Communication |
| Primary actor | Residents' Family, Nurse |
| Lanes | Residents' Family, Dien Hong system, Nurse |
| Inputs | W0/W1 rows FR-04-03 and FR-04-04. Context Diagram, Sequence Diagram and Architecture |
| Trigger | A family member or a nurse initiates a message. |
| Preconditions | No additional preconditions are assumed. |
| Success end state | The message is added to the relevant conversation, either with the nurses (family-initiated) or with the resident's family (nurse-initiated). |
| Failure end state | No message is added to the conversation |
| FRs covered | FR-04-03, FR-04-04 |
| Business rules applied | None. No explicit business rules are documented for UC-08 in the current W0/W1. |
| Version / status | v1.0 · Draft |
| Author | Trần Lê Bảo An |

1\. Diagram

Mermaid source:

```mermaid
swimlane-beta TB
  subgraph FAM["Residents' Family"]
    a1("Send message to nurses")
  end
  subgraph SYS["Dien Hong system"]
    start@{ shape: sm-circ }
    m0{" "}
    d1{"Who initiates<br>the message?"}
    s1("Add message to conversation with the nurses (FR-04-03)")
    s2("Add message to conversation with that family (FR-04-04)")
    m1{" "}
    d2{"Conversation<br>continues?"}
    stop1@{ shape: fr-circ }
  end
  subgraph NUR["Nurse"]
    a2("Send message to resident's family")
  end
  start --> m0 --> d1
  d1 -->|"[family member]"| a1 --> s1 --> m1
  d1 -->|"[nurse]"| a2 --> s2 --> m1
  m1 --> d2
  d2 -->|"[yes]"| m0
  d2 -->|"[no]"| stop1
```

&nbsp;

Reading notes:

* m0 and m1 are merge nodes. The loop from d2 \[yes\] returns into m0, not into d1 directly. The two alternative branches (family-initiated and nurse-initiated) are brought together at m1 before d2.

* There is no fork or join: no FR requires steps without order.

* Neither side is required to reply to a message. The diagram models each message as one initiation, and the loop only allows a further message.

* No message delivery, read receipts, online status, validation, access control or error handling is modelled, because none is in FR-04-03 or FR-04-04.

# **2\. Node trace**

| Node | Kind | Text | Traces to |
| :---- | :---- | :---- | :---- |
| d1 | Decision | Who initiates the message? | UC-09 trigger (family member or nurse) |
| s1 | Action | Add message to conversation with the nurses | FR-04-03 |
| s2 | Action | Add message to conversation with that family | FR-04-04 |
| d2 | Decision | Conversation continues? | N/A |

**3\. Branches and acceptance tests**.

| Branch | Given | When | Then |
| :---- | :---- | :---- | :---- |
| d1 \[family member\] | a family member of a resident | the family member sends a message | the message is added to the conversation with the nurses (FR-04-03) |
| d1 \[nurse\] | a nurse | the nurse sends a message to a resident's family | the message is added to the conversation with that family (FR-04-04) |
| d2 \[yes\] | a message has been added to a conversation | a family member or a nurse initiates another message | the flow returns through merge m0 and is handled as above; no reply is required from the recipient |
| d2 \[no\] | a message has been added to a conversation | no further message is initiated | the activity ends; no reply is required from the recipient |

# **4\. Object states used**

No object states have been specified for UC-08 in the current W0/W1.

# **5\. Screens touched**

| Actor action | Related FR row (W1) | Screen ID |
| :---- | :---- | :---- |
| Send message to nurses | FR-04-03-FE (chat in mobile app) |  |
| Send message to resident's family | FR-04-04-FE (chat on web system) |  |

# **6\. Open questions**

|  | Question | Blocking? | Default applied |
| :---- | :---- | :---- | :---- |
| 1 | Can the conversation continue after a message has been added? Neither FR-04-02 nor FR-04-03 specifies whether further messages can be exchanged. | No | The conversation can continue, as shown by the loop d2 \[yes\] → m0. Neither party is required to reply to every message. |
| 2 | \[NEEDS CLARIFICATION: Does a nurse need to be assigned to a resident, or linked to the resident's family, before they can exchange messages? FR-04-03 and FR-04-04 do not specify this requirement.\] | Yes | No access restrictions are included in the diagram. Any family member and any nurse can exchange messages. |
| 3 | \[NEEDS CLARIFICATION: If the system fails to add a sent message to the conversation, is the message considered sent, or should the sender be notified that the message was not added?\] | No | No failure flow is modelled. If the system fails to add the message to the conversation, the message is not added. |

# 7\. Verification log

| Check | Result | By | Date |
| :---- | :---- | :---- | :---- |
| Renders (mermaid-cli 11.16.0, swimlane-beta) | Pass |  |  |
| Every decision has guards on all outgoing flows | Pass |  |  |
| Every fork has a matching join | Pass (no forks) |  |  |
| Every system action cites an FR | Pass |  |  |
| Every FR of UC-09 appears in the diagram | Pass (2 of 2\) |  |  |
| Lane names match the allowed lane list | Pass |  |  |
| AI critic prompt run, findings resolved or logged | Pass |  |  |

&nbsp;
