# Context Pack: [TASK-###]

<!-- One compact pack per task (one page; book ch 12 Exercise 3). Include only what this task needs. Update after each review, before the next task. Delete sections that do not apply. [book ch 12, 25, App I; "Allowed files", "Completion check", "Source of truth", "Any file touched outside the allowed list", and SEC-### are additions]. Rule: specific, traceable, and current. [book App I] -->

## 1. Project background
Project name: [name]
Purpose: [what the system helps users or consumers do]
Primary users or consumers: [roles, systems, or callers]
Current stage: [intent | requirements | specification | task planning | testing | implementation | review | deployment | maintenance]
Avoid changing: [protected areas]

## 2. Current task
Task: [one focused task]
Source of truth: [REQ/AC/ADR IDs, spec section, task file]
Expected output: [what should be created or changed]
Allowed files / areas: [explicit list; not the same as the file map]
Do not change: [files, persisted data, features, decisions to protect]
Completion check: [how the work will be verified]
Stop condition: [when to stop and ask]

## 3. Relevant requirements
Requirement ID: [REQ-###]
Requirement statement: [what the system must do]
Acceptance criteria:
- [AC-### criterion]
- [AC-### criterion]
Tests to satisfy:
- [TEST-### scenario]

## 4. Technical and domain rules
Architecture rule: [relevant decision]
Data rule: [persisted data or model rule]
Interface rule: [contract, command, or API rule]
Security rule: [SEC-### or access rule]
Other domain rule: [anything specific to this project]

## 5. File map
[Only the folders and files relevant to this task, with a one-line purpose each.]

## 6. Project conventions
- [naming, function/module size, input validation, safe errors, tests for changed behavior]

## 7. Review rules
Before finishing, explain:
- What changed
- Which requirement was implemented
- Which tests should pass
- Any assumption made
- Any file touched outside the allowed list

## Update checklist (after review)
- [ ] Did the requirement change?
- [ ] Did a technical decision change?
- [ ] Did the file structure change?
- [ ] Is a new rule needed?
- [ ] Did an old rule become false?
- [ ] Does the next task need a smaller slice?

## Prompt to attach
Using this Context Pack, implement only the current task. Do not add unrelated features. Do not change protected files or decisions. After completing the work, summarize what changed, list the requirement implemented, and identify the tests that should pass.
