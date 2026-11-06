# Usage Flow: Dien Hong

  -----------------------------------------------------------------------
  Field                               Value
  ----------------------------------- -----------------------------------
  Product                             Dien Hong

  Version                             v0.1

  Date                                2026-11-06

## Before you draw: is Lesson 4 still true?

-   [ ] Every row of the Inputs check table at the top of
    `docs/activity/activity-index.md` has Status "Done", or is logged as
    an open question.

## Receptionist: create a new resident profile

``` mermaid
flowchart LR
  A(["Receptionist needs to register a new resident"]) --> U1["UC-01: Create new resident profile"]
  U1 --> Z(["New resident profile is created"])
```

  Step   Use case                            Activity diagram   Screens (fill in after Block 2)
  ------ ----------------------------------- ------------------ ---------------------------------
  1      UC-01 Create new resident profile   `AD-UC-01.md`      TBD

## Receptionist: update resident information

``` mermaid
flowchart LR
  A(["Receptionist needs to update an existing resident's information"]) --> U1["UC-02: Update resident information"]
  U1 --> Z(["Resident information is updated"])
```

  Step   Use case                            Activity diagram   Screens (fill in after Block 2)
  ------ ----------------------------------- ------------------ ---------------------------------
  1      UC-02 Update resident information   `AD-UC-02(1).md`   TBD

## Use cases without a flow

  Use case                           Started by   Described in
  ---------------------------------- ------------ --------------
  None identified for Receptionist   ---          ---

## Checks before you save

-   [x] Every actor that starts a use case has at least one flow.
-   [x] An actor with clearly different goals has one flow per goal; no
    flow has more than about 9 boxes.
-   [x] Every actor name matches the Use Case Diagram and the Context
    Diagram.
-   [x] Coverage: every in-scope use case of W0 Table 1 (Must, Should,
    Could) that a person starts appears in at least one flow, with the
    same ID and name. A use case may appear in several flows. Use cases
    started by time or an external system are listed under "Use cases
    without a flow". Won't-have use cases appear nowhere.
-   [x] No box is a click or a screen; no box tells two use cases.
-   [ ] Every flow renders on mermaid.live.
-   [ ] After Block 2: every step has at least one screen, and every
    screen in the screen list appears in some step.

------------------------------------------------------------------------

Template source: DBIZ2, VJCBI College - FTU, Lesson 8 (2026).
