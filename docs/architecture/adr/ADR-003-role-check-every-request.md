# ADR-003: The API Server checks the role on every request

| Field | Value |
|---|---|
| Status | Accepted |
| Date | Lesson 4 |

**Context.** Resident data is private. Each role may see only its own data: a family sees only
its own resident, a Nurse cannot change accounts, only the Admin sets roles (UC-12). The Admin
also needs access and action logs (UC-14).

**Decision.** After login, every request to the API Server carries a session token. The API
Server checks the token and the role permission before it does anything, and writes the access
and action to the log. The Web App only hides screens; it is never the only check.

**Consequences.** One place enforces permissions, so no screen can bypass them. Every use case
with "Login required = Yes" can assume the user is already logged in. Each request costs one
small extra check.
