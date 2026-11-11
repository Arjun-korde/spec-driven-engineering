# 07 - Debugging, code review, and refactoring

## Purpose
Handle defects and quality control with evidence and constraints instead of "try again" or "does it look okay". Debug from evidence, review code against the spec, and refactor in small steps that do not change behavior. The goal is not to reject AI coding; it is to use it with engineering control. [book ch19, ch20, App O, App P]

## When to use
- A check fails, a defect is reported, or behavior differs from the requirement (debugging).
- The agent has produced code that must be accepted or rejected (review).
- Structure no longer supports reliable change (refactoring).

## Inputs
- Requirement(s) and acceptance criteria, task, interface contract, test spec (whatever spec artifacts exist).
- Failure evidence: bug report, failing test, log line, error message, trace, or reproduction steps.
- The code or diff under review.
- The project's architecture or layering notes, if any (they override the generic layer list below).

## Output artifact
- `templates/debug-report.md` (one per defect, BUG-###).
- `templates/code-review-checklist.md` (one per review).
- A regression test (TEST-###) per fixed defect, and a spec or instruction update.

## Gate (done-when)
Debugging is done when: [book ch19 checklist, gate framing is addition]
- [ ] A check fails before the fix and passes after it.
- [ ] The requirement states the expected behavior clearly.
- [ ] The technical spec or interface contract states the system rule clearly.
- [ ] The persistent agent instruction warns against the previous mistake (whatever instruction file the project uses).
- [ ] The same bug cannot silently return in a future edit.

Review/refactor is done when: [book ch20 checklist, gate framing is addition]
- [ ] Every requirement is complete and fully implemented and traceable [book wording]; trace target is a REQ or TASK ID [addition].
- [ ] Each acceptance criterion has a matching automated or manual check.
- [ ] Boundaries, validation, security, and performance checks were applied in the review order below.
- [ ] Defects are written as review comments with severity, not vague complaints.
- [ ] After any refactor, the earlier checks still pass and behavior is unchanged.
- [ ] The spec is updated if the refactor produced a clearer structure.

## Part A - Debugging

### Workflow (one flow, seven steps)
Reproduce, collect evidence, compare to the requirement, diagnose, fix one cause, test, update the spec. [book ch19]

| Step | Action | Output |
|---|---|---|
| 1 | Reproduce the failure | A clear failure description |
| 2 | Collect logs, traces, failing checks | Evidence |
| 3 | Compare evidence to the requirement | Gap or contradiction |
| 4 | Ask the agent for root-cause analysis only (no code) | Diagnosis |
| 5 | Patch one cause at a time | Controlled fix |
| 6 | Run old and new tests (new regression test must fail before the fix) | Proof |
| 7 | Update spec and prevention note | Future guardrail |
[book ch19; the "fails before the fix" clause is moved here from the book's checklist to the workflow: addition]

### Rules
1. Never debug from memory alone. Write down the failure, the expected behavior, the actual behavior, and the evidence before you change code. [book ch19]
2. Do not start by asking the agent to rewrite or "try again"; first state what evidence you have. Otherwise the agent may guess in a new direction. [book ch19]
3. Logs show what the system was doing; a trace shows where a runtime error occurred. Neither gives the root cause by itself; the cause may be a missing validation rule or a wrong spec assumption. [book ch19]
4. Ask what the system received, what it expected, what it did, and where actual first differed from expected. [book ch19]
5. Order of reading a trace depends on the stack (some list the failure site first, some last). Find the failure site, then walk back to the requirement. [addition: the book says "bottom to top", which is stack-specific]
6. Name the failure pattern, then find where the instruction, context, code, or test coverage was incomplete. Typical patterns named in the book: misunderstood business rule, skipped validation, wrong data shape, happy-path only. [book ch19] (The book's pattern-to-first-evidence figure has no text; do not invent it.)
7. Fix one cause per change. [book ch19]
8. When the bug exposes a broken assumption, update four things together: code, test, requirement, agent instruction. [book ch19]
9. Every serious bug creates at least one new test, one note in the debugging record (`templates/debug-report.md`), and one correction to the requirement, technical spec, or agent instruction. A fix is complete only when code, tests, and specification agree. [book ch19] "Serious" is defined here as P0-P1; for P2-P3 the regression test is still required, the other two are recommended. [addition]
10. Evidence is not always a stack trace: for data, ops, documentation, or model work use the primary failure evidence (wrong output, diff, failing report). [addition]

### Evidence pack before asking [book App O]
- [ ] Expected behavior from the spec.
- [ ] Actual behavior observed.
- [ ] Failing test name and output.
- [ ] Relevant stack trace or log excerpt.
- [ ] Recent changes that may have introduced the issue.
- [ ] Files or modules likely involved.
Do not ask the agent to guess; give it logs, failing tests, expected and actual behavior, and the relevant specification. [book App O]

### Prompt: root cause [book ch19]
```
You are debugging one feature only.
Feature: [feature name]
Requirement ID: [REQ-###]
Expected behavior: [what should happen]
Actual behavior: [what happened]
Evidence: [log, trace, failing test, or error message]
Relevant files or functions: [list only what matters]
Task:
1. Explain the most likely root cause.
2. List two alternative causes to check.
3. Do not rewrite code yet.
4. Suggest the smallest safe fix.
5. State which test should prove the fix.
```
After reviewing the diagnosis, then ask for a small patch limited to that cause. Choose between candidate causes by which one the evidence can confirm or rule out cheapest. [book ch19; selection hint is addition]

### Prompt: evidence-only diagnosis [book App O, neutralized]
```
Here is the expected behavior, actual behavior, failing test output, logs, and relevant spec.
Identify the most likely root cause using evidence only.
Do not edit code yet.
Return: 1. probable root cause, 2. evidence, 3. files to inspect, 4. safest fix plan, 5. regression test to add.
```

### Root cause review [book App O]
- [ ] The proposed root cause is supported by evidence.
- [ ] The fix addresses the cause, not only the symptom.
- [ ] The fix does not weaken validation, tests, or security.
- [ ] A regression test is added or updated.
- [ ] The debugging record states the lesson learned.

### Broken-assumption table [book ch19]

| Assumption | Risk | Spec update | Test update |
|---|---|---|---|
| The looked-up item always exists | Null/empty-result failure | Define missing-item behavior | Test an unknown identifier |
| A credential or token is always valid | Unauthorized access | Define expiry/validity rule | Test an expired token |
| An input field is always present | Crash or bad data | Define required fields | Test a missing field |
| A value is always supplied | Weak validation | Define empty-input rule | Test an empty value |

Use the table to look for sibling assumptions in the same feature, not only the one that failed. [addition]

### Repeat-error prevention checklist [book ch19]
After a bug is fixed, confirm that:
- [ ] A test now fails before the fix and passes after the fix.
- [ ] The requirement explains the expected behavior clearly.
- [ ] The technical spec explains the system rule clearly.
- [ ] The agent instruction warns against the previous mistake.
- [ ] The same bug cannot silently return in a future edit.
- [ ] The fix does not weaken validation, tests, or security. [book App O]
The same lists are in `templates/debug-report.md`; tick them there.

### Worked note (illustration, one stack)
Expected: invalid credentials give a safe, generic error. Actual: an unknown identifier causes a server error. Evidence: the lookup result was null before the comparison. Root cause: code assumed the lookup always returns an item. Fix: check for a missing item before comparing. Regression test: unknown identifier returns the generic error. The deeper lesson: the requirement never defined the unknown-identifier behavior. [book ch19]
Other shapes of the same bug: CLI tool crashes on a missing file argument; data pipeline fails on an empty source batch; LLM feature errors on an empty retrieval result. [addition]

## Part B - Code review (review as an engineer, against the spec)

Mapping: a requirement says what behavior must exist; the technical spec says where it lives; the test plan says how it is verified; code review checks the implementation obeys all three. [book ch20]

Do not review by asking "does it look okay?" Ask "which requirement, design decision, and test does this code satisfy?" [book ch20]

### Review order (fixed)
1. Requirement fit
2. Design (boundaries and structure)
3. Security and validation
4. Performance
5. Readability
[book ch20 key-takeaway order is requirements, architecture, security, validation, performance, maintainability; consolidated to five steps with validation under security: addition]

Review requirement fit before style, cleverness, or framework choice. [book ch20]

### 1. Requirement fit [book ch20]

| Item | Question |
|---|---|
| Required behavior | Exact behavior from the requirement, not a guessed version? |
| Acceptance criteria | Can you point to the condition that proves it works? |
| Permitted roles | Does the code respect who may perform the action? |
| Failure path | Is the case where the requirement cannot be completed handled? |
| Traceability | Can you connect the code to a REQ or TASK ID? |

Working code is not requirement-complete code. [book ch20]

Prompt: requirement review [book ch20]
```
Review this code only against the listed requirements. Identify each requirement that is fully
satisfied, partially satisfied, missing, or implemented with extra behavior that was not requested.
Requirements: [list REQ-### with acceptance criteria]   <- this placeholder line is an addition
```
Traceability mechanism (the book gives none): put the REQ/TASK ID in the test name or the change description, and keep the traceability table current. [addition]

### 2. Design [book ch20]
Agents place code where it is easiest to generate, not where it belongs. Smells: request handling that contains deep business rules; data access scattered across unrelated files; validation hidden in random helpers.

| Layer (generic names) | Main responsibility |
|---|---|
| Interface / entry point | Receive input, call the logic layer, return the result |
| Validation | Reject invalid input before business logic runs |
| Business logic | Apply rules and coordinate the use case |
| Data access | Read and write persisted data through a clear boundary |
| Error mapping | Turn expected failures into safe, clear outcomes |
Use the project's own architecture notes when they differ. Refactor because the structure no longer supports reliable change, not because code is ugly. [book ch20]

### 3. Security and validation [book ch20]
Needs no expertise to start: who is allowed, what input is accepted or rejected, what is exposed on failure.

| Area | Question |
|---|---|
| Identity | Does the code confirm who the caller is? |
| Permission | Does it confirm what the caller may do? |
| Input validation | Are missing, malformed, or dangerous values rejected early? |
| Secrets | Are tokens, keys, credentials kept out of source and logs? |
| Errors | Are errors safe and useful (internal detail in logs only)? |

Prompt: security review [book ch20, neutralized; the severity scale is an addition, defined below]
```
Inspect this module for identity checks, permission checks, input validation, secrets exposure,
and unsafe error handling. Do not rewrite the code yet. Return a table of risks, severity
(use the scale: P0, P1, P2, P3), and suggested fixes.
```
Validation-versus-permission order: a decision to record, not a rule (see docs/resolved/07-09.md). Whatever order is chosen, a refusal must not leak whether protected data exists. [addition]
Also check that the agent did not verify the parent or owner object exists only by assuming it; a "partial" finding must end as fixed, deferred with an owner, or turned into a spec question, never silently dropped. [addition]

### 4. Performance [book ch20]
Not premature optimization. Refactor for performance only with a clear goal (faster response, lower cost, fewer failures, simpler scaling). Never ask the agent to "optimize everything" without a target.

| Risk | Check |
|---|---|
| Repeated work | Is the same query or call made inside a loop? |
| Overfetching | Does it load fields or records not needed? |
| Slow external calls | Does one request depend on many remote calls? |
| Missing limits | Can a caller request unlimited records? |
| Blocking work | Should heavy work move to a background job? |
Targets come from the project, recorded in the spec. [addition]

### 5. Readability [book ch20]
Names are clear and the code can be explained simply.

### Review checklist and self-check
Use `templates/code-review-checklist.md`, which carries the full App P checklist. App P adds these checks beyond the tables above: [book App P]
- [ ] The code implements only approved requirements and introduces no hidden product decisions.
- [ ] Duplicated logic is removed when safe.
- [ ] Error handling is explicit and tested.
- [ ] Logs are useful without exposing sensitive data.
- [ ] Tests verify business behavior, not only implementation details.
- [ ] Security-sensitive paths have negative tests.
- [ ] Refactoring does not change behavior unless the spec approves it.

 The book lets you paste the checklist into the prompt and have the agent evaluate its own output before manual inspection. That is a first pass only: the agent that wrote the code is not an independent reviewer, so a human or a separate fresh agent session must still review, especially security. [book ch20; independence note is addition]

### Severity scale for review comments [addition]
The book asks for "severity" but never defines it.

| Level | Meaning | Release effect |
|---|---|---|
| P0 | Security hole, data loss, or blocks release | Block |
| P1 | Major: requirement broken or missing with no workaround | Block unless approver accepts risk |
| P2 | Minor: requirement partly met, rare case, workaround exists | Fix or schedule |
| P3 | Cosmetic or naming | Optional |
Write each defect as a review comment: affected requirement or artifact, risk, severity, recommended fix, whether the fix changes the spec. [book ch25 field list; severity is addition]

## Part C - Safe refactoring

Refactoring improves internal structure without changing behavior. Freeze the expected behavior, change one part at a time, run tests, review the difference, then continue. If the agent changes behavior, stop and correct the instruction. [book ch20] If behavior changes, it is a requirement or design change and must be documented [book ch25]; record it as a CHG-### using `templates/spec-change-record.md` [addition].

### Safe loop [book ch20]
1. Start with a passing test or a clear statement of expected behavior.
2. Choose one refactoring goal (for example, separate validation from business logic).
3. Ask the agent to modify only the selected file or function.
4. Run the relevant checks and compare behavior before and after.
5. Accept the change only if behavior remains correct.

### Prompt: safe refactor [book ch20]
```
Refactor only [selected module or function]. Preserve all existing behavior and result shapes.
Do not add new features. [Refactoring goal, e.g. separate validation from business logic.]
After the refactor, explain what changed and list which tests should still pass.
```

### Unsafe refactor requests (do not send) [addition]
Broad "clean up everything"; no passing check to compare against; multiple goals in one request; a refactor mixed with a bug fix or feature; unbounded file scope.

### Practical workflow (review then refactor) [book ch20]
1. Identify the requirement and acceptance criteria behind the code.
2. Review for requirement fit before judging style.
3. Check boundaries and security rules.
4. List defects as review comments, not vague complaints.
5. Choose one safe refactoring goal.
6. Ask the agent to refactor only that area.
7. Run tests and compare behavior before accepting.
8. Update the spec if the refactor introduces a clearer structure.

### Worked review (illustration, one stack)
A draft "create item inside a parent" handler fetched the caller but never checked membership, accepted an empty title, and did not verify the parent. Review result: title required = Missing; only members may create = Missing; item belongs to selected parent = Partial; controlled errors = Missing. After refactor: validation function, permission check, logic function, and a thin entry point mapping validation errors and permission errors to distinct outcomes. Post-refactor checks: empty title gives the validation outcome; non-member gets the permission outcome; member succeeds; entry point only delegates. [book ch20]
Cautions seen in the book's sample (apply to any language): a default-value-plus-trim pattern can still fail on an explicit null; a built-in exception type reused as a domain error is confusing; the "Partial" parent check was left without a test. Add those tests. [book ch20, cautions are addition]
Other shapes: CLI command with validation split from the action; pipeline stage with parsing split from loading; LLM feature with prompt building split from calling. [addition]

## Common mistakes
- Asking the agent to "fix it" or "try again" without evidence.
- Fixing the symptom while the requirement stays silent on the case.
- Fixing several causes in one change.
- No regression test, or a regression test that never failed.
- Reviewing style first, requirement fit last.
- Treating the agent's self-review as the review.
- Refactor and behavior change in one step.
- Broad cleanup prompts.

Book inconsistencies resolved here are listed in docs/resolved/07-09.md.

## Role notes [addition]
- Requester/PM: confirms the expected behavior when a bug reveals an unspecified case; approves the resulting spec change.
- Builder: supplies evidence, runs the root-cause prompt, limits each fix and refactor to one cause or goal, adds the regression test.
- Reviewer/QA: reviews in the fixed order, assigns severity, verifies the regression test failed before the fix, and rejects behavior changes hidden in refactors.
