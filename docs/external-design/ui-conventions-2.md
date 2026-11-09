# UI Conventions: Dien Hong System

> Aligned with the client's public website duonglaodienhong.vn (read on 09/11/2026) for language,
> forms of address and formats, and with the Screen List, W1 and the activity diagrams of this
> repository for components and messages.

## 1. Language and tone

- Interface language: Vietnamese, like the client's website (`vi_VN`). Labels in this repository
  are written in English; every label and message below has its Vietnamese version in the
  translation file.
- Address staff (Receptionist, Nurse, BOD, Admin) as "bạn". Address family members as "quý khách",
  the form the website uses ("Chào mừng quý khách đến với Diên Hồng").
- In Vietnamese text for families, a resident is "cụ" + full name ("cụ Nguyễn Văn An"), as the
  website says "các cụ". Staff screens show the full name and the room.
- Messages from the nursing home to families speak as "Diên Hồng".
- Button labels are verbs that say what happens: "Update condition", "Acknowledge", "Close alert",
  "Deactivate account", "Assign role". Never "OK", "Submit" or "Xác nhận" alone.
- Alert texts are calm and factual: who, where, when, and what is being done.

## 2. Formats

| Item | Format | Example |
|---|---|---|
| Date | dd/MM/yyyy | 09/11/2026 |
| Time | HH:mm, 24-hour | 14:32 |
| Date and time | dd/MM/yyyy HH:mm | 09/11/2026 14:32 |
| Period | dd/MM/yyyy – dd/MM/yyyy | 01/11/2026 – 30/11/2026 |
| Duration | m min ss s | 1 min 20 s |
| Phone number | 10 digits in groups 4.3.3 with dots, no country code | 0333.118.504 |
| Room | "Room" + number (Vietnamese: "Phòng") | Room 204 |
| Resident on staff screens | full name · room | Nguyễn Văn An · Room 204 |
| Alert status | New, Acknowledged, Escalated, Closed, spelled exactly as in AD-UC-06 | Escalated |
| Notice count | "Notice" n "of 3" | Notice 2 of 3 |
| Radar status | Online or Offline, then the time of the last data | Offline · last data 09/11/2026 14:02 |
| Account status | Active or Inactive | Inactive |

The website writes dates in two ways ("21/9/2014", "21.02.2026") and times as "09h00"; the web app
uses only the formats above, so every timestamp in alerts, condition records and logs looks the
same.

## 3. Components

| Component | When to use | Notes |
|---|---|---|
| Primary button | the one main action of a screen | one per screen, full width on mobile, at least 44px tall |
| Secondary button | alternative actions such as Cancel and Back | outline style |
| Real-time alert pop-up | a new or repeated alert, on every signed-in nurse screen (FR-03-02, FR-03-04) | alert colour, sound, resident, location and time; one primary button "Acknowledge" |
| Status badge | the state of an alert, a radar or an account | text AND icon AND colour, never colour alone |
| Timeline | condition history of one resident (FR-02-04) | records in time order; each shows the date and time and the nurse's name (FR-02-02) |
| Chat bubble | messages between a family and the nurses (UC-09) | own messages on the right, the time under each message |
| Modal | confirm or cancel a single action on one item | Close alert with outcome, Deactivate account; never used for navigation |
| Banner | non-blocking information or an empty state | e.g. "No condition has been recorded yet for cụ Nguyễn Văn An" |
| Inline field error | a field that failed validation (FR-01-01, FR-01-02, FR-06-04) | under the field, one message per field |
| Toast | short confirmation after success | e.g. after an account is created, edited or deactivated (`AD-UC-11` S4); disappears after 4 seconds |
| Filter bar | period on the report (FR-05-01); user and date on the logs (FR-06-12) | the applied filter stays visible above the list |

Status badges:

| State | Colour token | Icon |
|---|---|---|
| Alert New | colour/alert | bell |
| Alert Escalated | colour/alert | bell with "!" and the notice count |
| Alert Acknowledged | colour/caution | person with a check mark |
| Alert Closed | colour/text-muted | check mark |
| Radar Online | colour/success | dot |
| Radar Offline | colour/caution, row highlighted (FR-06-10) | crossed-out signal |
| Account Active | colour/success | dot |
| Account Inactive | colour/text-muted | lock |

Red (colour/alert) is used only for resident alerts, so a form error never looks like an emergency.

## 4. Messages

| Situation | Pattern | Example |
|---|---|---|
| Sign-in refused (FR-00-01) | what happened + what to do | "The account or password is not correct, or the account is no longer active. Try again or contact the Admin." |
| Access denied (FR-06-03) | what happened + where you are now | "You do not have access to that function. You are back on your home screen." |
| Resident not linked (FR-04-01) | what happened | "You can only see the residents linked to your account." |
| Invalid resident details (FR-01-01, FR-01-02) | field + what to do | "Enter the full name." |
| Condition rejected (FR-02-01) | why + what to do | "The condition is empty. Enter the condition and save again." |
| No condition yet (FR-04-02) | what exists | "No condition has been recorded yet for cụ Nguyễn Văn An." |
| New alert, nurse pop-up (FR-03-02) | who + where + when | "Abnormal event: Nguyễn Văn An, Room 204, 14:32." |
| Repeated notice (FR-03-04) | count + what to do | "Not acknowledged yet (notice 2 of 3). Acknowledge it if you are handling it." |
| First notice to the family (FR-03-09) | what happened + what is being done | "Diên Hồng: an abnormal event was detected for cụ Nguyễn Văn An at 14:32. The nurses have been notified." |
| Closed notice to the family (FR-03-10) | outcome | "Diên Hồng: the alert for cụ Nguyễn Văn An at 14:32 is closed. Outcome: false alarm." |
| Identifier already in use (FR-06-04) | what happened + what to do | "This identifier is already in use. Choose another one." |
| Family account without a resident (FR-06-04) | what to do | "Select at least one resident for a family account." |
| Confirm deactivation (`AD-UC-11` D6) | consequence + no undo | "Deactivate nurse03? This person is signed out at once and can no longer sign in. This cannot be undone." |
| Role outside the permitted scope (`AD-UC-12` S5) | what happened + what to do | "You cannot assign this role. Choose another role." |
| Radar status unavailable (FR-06-11) | what happened, no old data shown | "The radar status cannot be shown right now. Try again later." |
| Network error | what happened + retry | "Not saved yet. Check your connection and try again." |

## 5. Accessibility minimums

- Body text contrast at least 4.5:1 (WCAG 2.2, 1.4.3); icons, badge borders and field borders at
  least 3:1 (1.4.11).
- Alert, radar and account states never shown by colour alone (WCAG 2.2, 1.4.1): every state also
  has a text and an icon.
- The alert sound never carries information on its own: the pop-up shows the same resident,
  location and time. The sound stops when the alert is acknowledged and plays for at most
  3 seconds per notice (WCAG 2.2, 1.4.2).
- Targets at least 24 by 24 CSS pixels (WCAG 2.2, 2.5.8); primary buttons at least 44px tall.
- Zoom stays allowed: never set `user-scalable=no` or `maximum-scale=1` (WCAG 2.2, 1.4.4). The
  client's website sets both; the web app must not.
- Page language set to Vietnamese, `lang="vi"` (WCAG 2.2, 3.1.1).
- Smallest supported width: 360px (one responsive web app for phone and computer, ADR-001).

## 6. Design tokens

Every Dien Hong mockup uses only these values. The wireframes are drawn at 360px and exported at
3x, so every image in `screens/img/` is 1080 pixels wide.

| Token | Value | Used for |
|---|---|---|
| colour/primary | #1D6B57 | primary button, links, selected item |
| colour/text | #1F2933 | titles |
| colour/text-body | #3F4A54 | body text, chat bubbles, timeline entries |
| colour/text-muted | #5B6670 | timestamps, hints, Closed and Inactive badges |
| colour/border | #848E97 | field borders, secondary button |
| colour/border-light | #D5DBDF | card and banner borders, dividers |
| colour/surface | #F4F6F7 | banner background, other person's chat bubble |
| colour/skeleton | #EEF1F3 | loading placeholders |
| colour/disabled | #C5CDD3 | disabled primary button |
| colour/alert | #B42318 | real-time alert pop-up header, New and Escalated badges |
| colour/alert-text | #7A271A | alert text on a light background |
| colour/alert-bg | #FEF3F2 | alert rows in the alert list |
| colour/caution | #B45309 | field error border, Acknowledged badge, Offline radar highlight |
| colour/caution-text | #7C3A06 | field error text, Offline radar text |
| colour/caution-bg | #FDF3E3 | field error background, Offline radar row |
| colour/success | #067647 | Online and Active badges, toast check mark |
| colour/toast-bg | #1F2933 | toast background |
| font/family | Be Vietnam Pro, fallback Roboto, Arial | all text (full Vietnamese diacritics) |
| font/size | title 22px, body 16px, small 14px, button 16px | |
| space/unit | 4px; screen padding 16px, list gap 8px | padding, gaps |
| radius | primary button 10px, card and banner 8px, badge 999px | corners |
| frame/width | 360px | every mockup is drawn at this width |

colour/primary is provisional: the website's stylesheet could not be read when this file was
written. Replace it with the main colour of duonglaodienhong.vn and check its row in the table
below again.

Contrast, checked with the WCAG 2.2 formula:

| Pair | Ratio | Needed |
|---|---|---|
| text #1F2933 on white | 14.8:1 | 4.5:1 (1.4.3) |
| text-body #3F4A54 on white | 9.1:1 | 4.5:1 (1.4.3) |
| text-muted #5B6670 on white | 5.9:1 | 4.5:1 (1.4.3) |
| text-muted #5B6670 on surface #F4F6F7 | 5.4:1 | 4.5:1 (1.4.3) |
| white on primary #1D6B57 | 6.4:1 | 4.5:1 (1.4.3) |
| white on alert #B42318 | 6.6:1 | 4.5:1 (1.4.3) |
| alert-text #7A271A on alert-bg #FEF3F2 | 9.1:1 | 4.5:1 (1.4.3) |
| caution-text #7C3A06 on caution-bg #FDF3E3 | 7.8:1 | 4.5:1 (1.4.3) |
| caution #B45309 on white (field error border) | 5.0:1 | 3:1 (1.4.11) |
| success #067647 on white | 5.7:1 | 4.5:1 (1.4.3) |
| white on toast-bg #1F2933 | 14.8:1 | 4.5:1 (1.4.3) |
| border #848E97 on white (field edge) | 3.3:1 | 3:1 (1.4.11) |

The disabled button (white on #C5CDD3) is exempt: WCAG 1.4.3 does not apply to inactive
components.
