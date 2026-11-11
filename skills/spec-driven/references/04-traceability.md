# 04 - Traceability

**Purpose:** Connect requirement -> design -> task -> test -> code -> status so nothing is lost. "A requirement is a promise. Traceability is how you prove that the promise did not disappear while the software was being built." Agent output can look complete while quietly missing behavior; traceability creates checkpoints, not a slower build. [book ch10]

**When to use:** Once requirements and technical design exist and before the agent starts building; update on every change, merge, and spec change.

**Inputs:** Approved REQ-### (with AC-###), technical spec / ADR-### / contracts, task list (TASK-###), test plan (TEST-###).

**Output artifact:** `traceability-matrix.md` (template: `templates/traceability-matrix.md`), kept next to the spec so it stays maintained. Needs no special software; a plain table is enough. [book ch10]

**Gate (done-when):** [book ch10 Traceability Review Checklist and App F checklist; adapted, additions tagged per item]
- [ ] Every important requirement has a unique REQ-###. [book ch10]
- [ ] Every requirement links to at least one design reference (ADR-###, contract, or spec section), or is marked "single obvious implementation". [book ch10, adapted]
- [ ] Every design reference links to one or more small tasks. [book ch10]
- [ ] Every task links to at least one test. [book ch10] Tests are planned before implementation. [addition]
- [ ] Every AC-### is covered by at least one test. [addition]
- [ ] Every implemented feature has a code/artifact link. [book ch10]
- [ ] Any code without a requirement is removed, documented, or approved. [book ch10]
- [ ] Every blank matrix cell has been reviewed and either filled or justified. [book ch10]
- [ ] Every Must requirement has at least one task and at least one test. [book App F]
- [ ] Every security rule (SEC-###) maps to validation or authorization code. [book App F]
- [ ] Every released feature maps back to a requirement. [book App F]
- [ ] Every changed behavior is reflected in updated specs. [book App F]
- [ ] A row is "done" only when its tests have Result = Pass with evidence. [addition]

---

## 1. The chain [book ch10]

| Link | Question |
|---|---|
| Requirement | What must the system do? |
| Design reference | How will the system support it? |
| Task | What work must be completed? |
| Test | How will you verify it? |
| Code/artifact link | Where is it implemented? |
| Status | Is the chain complete and approved? |

Rules:
- Requirement = what; design decision = how. If a requirement could be implemented in multiple ways, document the chosen direction before writing tasks. [book ch10]
- Tasks are narrow enough that the agent completes them without guessing across the project; link each to its design reference. [book ch10]
- "Every task should have at least one test that proves it was completed correctly." A task tells the agent what to do; a test tells you whether the result is acceptable. [book ch10]
- Control point: never ask the agent to "just build the feature" when the requirement has no linked task and test. [book ch10]
- Code links may be file, function, module, or other short reference; for non-code work use config key, document section, dashboard, or runbook. [book ch10; addition on non-code]
- "Test" means any verification: automated check, manual check with recorded steps, review, or metric. Prefer automated. [addition]
- Matrix lives in the spec document or wherever the spec lives (markdown, tracker). [book ch10]

### IDs and vocabularies [addition: resolves book inconsistencies]
- `REQ-###`, `AC-###`, `ADR-###`, `TASK-###`, `TEST-###`, always 3 digits, no area codes in the ID (put area/type in a column). The book mixes 2-digit and 3-digit and area-coded IDs.
- Task status: todo | doing | blocked | review | done.
- Review status of the row: Needs review | Approved | Needs update.
- Test state is two fields: Plan state = Planned | Written | Automated | Manual | Blocked; Result = Not run | Pass | Fail. One vocabulary per concept; do not mix "Draft/Ready/Done", "Passing", and "Ready for review".

### Row status transitions [addition]
| Row review status | Meaning | Move to it when |
|---|---|---|
| Needs review | Chain filled, awaiting check | Agent/Builder finishes a task or edit |
| Approved | Reviewer verified chain and test evidence | Checklist passes and tests pass |
| Needs update | Gap found or spec changed | Any gap, failing test, or CHG-### affecting the row |

## 2. Gap detection [book ch10]

| Gap | Meaning | Action |
|---|---|---|
| Requirement has no design link | Approach unclear | Write or confirm the design decision |
| Design has no task | Work not planned | Create a small agent-friendly task |
| Task has no test | Cannot verify completion | Write at least one test case |
| Test has no code link | Implementation missing or hard to locate | Add file/function reference after implementation |
| Code has no requirement | Agent may have added unapproved behavior | Remove it, or document and approve it |
| Security rule has no validation/authorization code | Rule may be unenforced [book App F] | Link the code and a SEC-### test, or implement it |

Agent risks the matrix catches [book ch10]:
| Risk | Check |
|---|---|
| Builds a related but wrong feature | Task links to the exact requirement |
| Skips an edge case | AC produced a test case |
| Passes basic tests but breaks a rule | Business rules appear in test links |
| Silently changes architecture | Code still follows the design decision |

Additions [addition]:
- Mapping rules: the matrix has an AC column; a TEST-### verifies one or more AC-### (or the REQ when it has no AC); an AC may map to a different TASK than its REQ (give it its own row); this matrix is the single place the mapping lives (test-spec, task files, and the technical spec link here and keep forward TASK/TEST IDs as "filled in later" placeholders until the matrix is filled).
- A gap found here (e.g. an AC is missing) reopens the earlier artifact through a CHG-### (`templates/spec-change-record.md`); re-run that gate for the affected item only.
- Missing behavior discovered during building (e.g. session expiry, rate limiting) becomes a NEW REQ-### with its own task and test, never a hidden assumption in an existing row. [book ch10 principle, formalized]
- Each AC gets its own test reference (put AC in the matrix as a column or sub-row); the book's matrix omits AC.
- Minimum tests per feature before building: at least one normal, one edge, one failure case. [book ch17]
- A requirement with no test, no task, or no review evidence is not ready for production. [book ch28]

## 3. Maintaining the matrix [addition, from book ch28 workflow]
- Update on each task merge: link code, record test evidence, set status. [book ch28: merge requires updated traceability]
- On a spec change (CHG-###): find affected rows by REQ, mark them Needs update, adjust tasks/tests, then code.
- If behavior changed in code, update the spec; if the spec changed, update code (spec drift is always resolved one way or the other). [book ch27, ch15]
- Generate task lists only from approved requirements. [book ch10 exercise]

## 4. Review checklist [book ch10]
Use the Gate list above. Additionally:
- [ ] Task-to-test mapping is complete: no task without a TEST-###.
- [ ] Error behavior in tests matches the project's single error shape (see 03-technical-design.md).
- [ ] No test or task refers to an ID that does not exist.

## 5. Worked examples [addition: book's login example generalized; its gaps fixed]

The book's login example left TASK-AUTH-001 with no test, used a bare-string error unlike its contract, and marked the row ready for review. The examples below give every task a test and use the project's error shape.

### Example A: Web API stack - sign-in
| REQ | Requirement | AC | Design ref | Task | Test | Code link | Task status | Plan state / Result | Review status |
|---|---|---|---|---|---|---|---|---|---|
| REQ-001 | A registered user can sign in with email and password | AC-001 valid credentials return a short-lived session token; AC-002 invalid credentials return a generic error | ADR-001 (server-side auth, hashed passwords); contract `sign-in` | TASK-001 Define sign-in contract | TEST-001 contract example matches schema (normal) | `docs/interface-contract.md` (sign-in section) | done | Automated / Pass | Approved |
| | | | | TASK-002 Validate input | TEST-002 missing password -> VALIDATION_ERROR (failure) | `auth/login.py -> validate_login()` | done | Automated / Pass | Approved |
| | | | | TASK-003 Verify hash, issue token | TEST-003 valid credentials -> token (normal) | `auth/login.py -> login_user()` | review | Automated / Not run | Needs review |
| | | | | TASK-004 Generic failure | TEST-004 wrong password and unknown email -> same message (failure/edge) | `auth/login.py -> login_user()` | review | Automated / Not run | Needs review |
| REQ-002 | Sessions expire (new requirement found during build) | AC-003 expired token is rejected | ADR-001 | TASK-005 Reject expired token | TEST-005 expired token -> UNAUTHORIZED (edge) | - (not built yet) | todo | Planned / Not run | Needs update |

Examples show Plan state and Result in one cell; the template keeps them as separate columns.

Error shape in TEST-002/004: `{ "error": { "code": "...", "message": "...", "field"?: "..." } }`.

### Example B: CLI / data tool stack - CSV export command
| REQ | Requirement | AC | Design ref | Task | Test | Code link | Task status | Plan state / Result | Review status |
|---|---|---|---|---|---|---|---|---|---|
| REQ-010 | `report export` writes the selected records to a CSV file | AC-010 writes header + rows for valid input; AC-011 refuses to overwrite without `--force`; AC-012 empty selection writes header only | ADR-004 (streaming writes); contract `report export` (args, exit codes) | TASK-020 Parse args and validate | TEST-020 unknown flag -> exit code 2 + `error[INVALID_ARGUMENT]` on stderr (failure) | `cli/args.py` | done | Automated / Pass | Approved |
| | | | | TASK-021 Write CSV | TEST-021 3 records -> header + 3 rows (normal) | `export/csv_writer.py -> write_rows()` | done | Automated / Pass | Approved |
| | | | | TASK-022 Overwrite protection | TEST-022 existing file without `--force` -> exit code 3, file unchanged (failure) | `export/csv_writer.py -> open_target()` | review | Automated / Not run | Needs review |
| | | | | TASK-023 Empty selection | TEST-023 0 records -> header only, exit code 0 (edge) | `export/csv_writer.py -> write_rows()` | doing | Written / Fail | Needs update |

The same chain works for libraries (code link = exported function), pipelines (code link = job/step name), and LLM features (test = grounding check with a fixed input and an expected refusal).

## Prompts

### P4.1 Generate a first traceability matrix [book ch10, adapted: "design reference" wording, the normal/edge/failure clause, and the ID-format sentence are additions]
```
Using the requirements and technical specification below, create a Requirements Traceability Matrix.
For each requirement, include a requirement ID, linked design reference (ADR-###, contract, or spec section),
suggested engineering task, suggested test case (at least one normal, one edge, one failure where
applicable), likely code area, and status. Use IDs of the form REQ-###, TASK-###, TEST-###, ADR-###.
Do not invent features outside the provided specification.
```

### P4.2 Gap check [addition]
```
Audit the traceability matrix below against the requirements and specification. List: requirements
without a design reference; design references without tasks; tasks without tests; acceptance criteria
without tests; tests without code links; code or tasks without a requirement; IDs that are duplicated,
missing, or not in the standard format. For each gap give the smallest action to close it. Do not
change the matrix until you have listed the gaps.
```

### P4.3 Tasks from approved requirements [book ch10 exercise, phrased]
```
Generate implementation tasks only from the approved requirements below. For each task give: TASK-###,
linked REQ-###, linked design reference, expected output, and at least one TEST-### that proves it is
done. Do not create tasks for anything not in an approved requirement; list any missing requirement
as a question instead.
```

## Common mistakes
- Task with no test; row marked ready anyway. [book ch10; addition]
- Hiding new behavior inside an existing row instead of adding a requirement. [book ch10]
- Matrix kept in a separate tool nobody updates. [book ch10]
- Mixed ID formats and mixed status vocabularies. [addition]
- Blank cells left unreviewed. [book ch10]

Book inconsistencies resolved here are listed in docs/resolved/04-traceability.md.

Role notes: [addition] Requester/PM confirms every REQ has a verifiable test and approves new REQs for found gaps; Builder/Agent fills links as work lands and never marks a row Approved; Reviewer/QA runs the gap check and checklist and sets Approved or Needs update; Approver signs off before release.
