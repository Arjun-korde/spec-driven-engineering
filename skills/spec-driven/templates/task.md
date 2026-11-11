# TASK-###: [short task title]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
One file per task; the index of tasks is templates/task-list.md. [addition]

<!-- Merged from the book's 15-field, 9-field, 7-section, and ch 1 task cards [book ch 1, 4, 14, 16] plus stop condition [book ch 25]. Delete sections that do not apply. -->

Status: todo | doing | blocked | review | done
Priority: P0 | P1 | P2 | P3
Owner: [person or agent]

## Source
Source requirement / spec section: [REQ-###, AC-###, ADR-###, interface contract section]
Business reason: [why this is needed]

## Goal
[One outcome. If there is more than one major outcome, split the task.]

## Inputs and outputs
Inputs: [spec sections, files, test notes the agent should use]
Outputs / expected output: [what must exist after the task]
Dependencies: [TASK-### that must be done first, or None]

## Scope
Allowed files / areas (hard list): 
- [path or artifact]
Not allowed to change (hard list):
- [path, behavior, or decision]
Out of scope:
- [feature or work that must not be done here]
Constraints: [architecture, interface, style, no new dependencies unless the spec allows]

## Instructions
Implementation notes / steps:
1. [step]
2. [step]

## Done and verification
Done criteria (checkable):
- [ ] [criterion]
Tests (planned before implementation; >=1 normal, >=1 edge, >=1 failure):
- [TEST-### scenario -> expected result]
Stop condition: [when the agent must stop and ask, e.g. "Do not modify [area] unless a failing test proves it is necessary"; also stop if any file outside the allowed list seems necessary]

## Review checklist
- [ ] Behavior matches the source requirement and ACs
- [ ] Follows the technical design and constraints
- [ ] Tests prove success and failure paths
- [ ] No unrelated changes; changed files are within the allowed list
- [ ] Specs and traceability updated if a decision changed

## Open questions
- [question for the Requester/Approver; do not invent behavior]

## Completion report (agent fills in)
Files changed and why: 
Requirement / AC covered: 
Tests added or updated, and results: 
Assumptions: 
Risks: 
Unresolved questions: 
