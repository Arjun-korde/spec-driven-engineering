# Traceability Matrix: [project or feature]

Delete rows/columns that do not apply. Keep this file next to the spec (docs/traceability-matrix.md). Chain from [book ch10] and [book App F]; AC column, split statuses, and 3-digit IDs are [addition].

IDs: REQ-###, AC-###, ADR-###, TASK-###, TEST-###. Task status: todo | doing | blocked | review | done. Review status: Needs review | Approved | Needs update. Test Plan state: Planned | Written | Automated | Manual | Blocked. Test Result: Not run | Pass | Fail.

| REQ | Requirement (one sentence) | AC | Design ref / spec section (ADR / contract) | TASK | TEST (planned before build) | Code area / artifact link | Task status | Plan state | Result | Review status |
|---|---|---|---|---|---|---|---|---|---|---|
| REQ-### | [ ] | AC-### | ADR-### / [contract] | TASK-### | TEST-### | [file -> function / config / doc section] | todo | Planned | Not run | Needs review |
| REQ-### | [ ] | AC-### | | TASK-### | TEST-### | | todo | Planned | Not run | Needs review |

One row per AC-to-task link (repeat the REQ id or leave blank under the same REQ). A TEST verifies one or more AC-### (or the REQ when it has no AC). An AC may belong to a different TASK than its REQ's main task (e.g. an empty-list AC built in the config task): give that AC its own row. This file is the ONLY place the REQ/AC/TASK/TEST mapping lives; other files link here and do not copy it. Every task has at least one TEST; every AC is covered by a TEST. [addition]

## Gap check (run on every change)
| Gap | Action |
|---|---|
| Requirement has no design ref | Write or confirm the design decision (or state "single obvious implementation") |
| Design ref has no task | Create a small agent-friendly task |
| Task has no test | Write at least one test [addition: normal / edge / failure across the feature] |
| AC has no test | Add a test [addition] |
| Test has no code link | Add link after implementation |
| Code has no requirement | Remove it, or document and approve it |
| Security rule (SEC-###) has no validation/authorization code | Link the code and a test, or implement it [book App F] |
| Blank cell | Review; fill or justify |

## Review checklist
- [ ] Every important requirement has a unique REQ-###.
- [ ] Every requirement links to a design reference (or states a single obvious implementation).
- [ ] Every design reference links to one or more small tasks.
- [ ] Every task links to at least one test; every AC is covered. [AC coverage: addition]
- [ ] Every Must requirement has at least one task and one test. [book App F]
- [ ] Every security rule maps to validation or authorization code. [book App F]
- [ ] Every implemented or released feature has a code/artifact link and maps back to a requirement.
- [ ] Code without a requirement is removed, documented, or approved.
- [ ] Every changed behavior is reflected in updated specs. [book App F]
- [ ] Newly found behavior is a new REQ-###, not a hidden assumption in an existing row.
- [ ] A row is Approved only with Result = Pass and evidence.

## Change log (records are in docs/changes/; see templates/spec-change-record.md)
| CHG-### | Date | Rows affected | Summary |
|---|---|---|---|
