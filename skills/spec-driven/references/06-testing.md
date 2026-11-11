# 06 - Testing: plan before code, generate with AI, review the result

## Purpose
Define "done" as checkable evidence before any implementation exists, then use the agent to turn that plan into real checks without letting it invent behavior. A test plan is a quality gate before the code stage; tests planned first check what the requirement promised, tests written after the code tend to check what the code does. [book ch17]

## When to use
- Before asking the agent to build any feature, fix, or change.
- When the agent has produced tests and you must judge them.
- When a requirement has no linked check (traceability gap).

## Inputs
- The requirement (REQ-###) and its acceptance criteria (AC-###).
- The interface contract or design rule that applies, if one exists.
- The project's own test levels and tooling (use whatever the project has; this file names no tool).
- Project-set targets for performance or limits (the project decides and records them; never copy numbers from examples). [addition]

## Output artifact
`templates/test-spec.md` filled in: one row per check, each linked to a REQ/AC, each with a level, a case category, an expected result, a failure meaning, an owner, a Plan state, and a Result.

## Gate (done-when)
Test planning is done when: [addition, derived from book ch17/ch18 checklists]
- [ ] Every REQ in scope has at least 1 normal, 1 edge, and 1 failure check. [book ch17]
- [ ] Each check names its REQ/AC, level, input/action, expected result, failure meaning, and risk covered; each has a Plan state and a Result.
- [ ] Applicable security/abuse and isolation-boundary checks are listed, or "not applicable" is recorded with a reason.
- [ ] Performance or limit checks reference a target recorded by the project, or are marked "no target yet" (not invented).
- [ ] Checks were planned before implementation and any open decision (see "Decisions to record") is written down.
- [ ] After generation: every generated test was labeled strong / weak / missing / invented and the labels were resolved. [book ch18]

## Core rules
1. Do not ask the agent to build a feature until you can write at least three checks for it: one normal, one edge, one failure. [book ch17]
2. "Three" is the floor, not a target. Add checks until each acceptance criterion and each identified risk is covered. [addition: the book gives no upper rule]
3. Plan tests in plain language first; the plan must be clear enough that a human or the agent can create the real tests from it. [book ch17]
4. Tests are planned before implementation and written first when practical; never planned after approval. [addition]
5. Security tests matter most for AI code: the agent may implement the happy path and forget the denial path. [book ch17]
6. If a user or consumer would complain loudly when a flow breaks, that flow needs an end-to-end plan. Keep end-to-end checks few; do not cover every small rule with them. [book ch17]
7. Do not accept tests because they look professional. A useful test has a clear purpose, realistic input, a strong assertion, and a link to an acceptance criterion. [book ch18]
8. Do not add behavior that is not in the spec (examples: lockouts, rate limits, trimming, expiry). Either leave it out or update the spec first. [book ch18]
9. If planning exposes a missing AC or REQ (an edge case with nothing to verify), reopen the requirements: record a CHG-### (`templates/spec-change-record.md`), add the AC, and re-run the requirements gate for that item only. Do not add an unbacked test. A TEST-### verifies one or more AC-###; the mapping lives only in the traceability matrix (see 04). [addition]

## One test taxonomy (levels vs case categories)
The book mixes these in one list. Keep them as two separate fields. [addition: resolves book ch17 mixed taxonomy]

Level (what scope the check exercises). Use the project's own names where they differ.

| Level | Checks | Use for | Stack illustrations (one line each) |
|---|---|---|---|
| Unit | One rule, function, or component alone | Calculations, validators, permission checks, formatting | Web API validator; CLI argument parser; library function; pipeline transform; prompt template builder |
| Integration | Two or more parts together | Entry point + state store; identity + action; validation + response handling; service + external dependency (success, failure, timeout) | API + persisted data; CLI + file system; pipeline stage + source; LLM feature + model call |
| End-to-end flow | A whole user or consumer journey | The few flows that must never break | Sign-up to first result; install and run command; nightly job from input to published output |
| Security / abuse | Denial paths, hostile input, leakage | Who may do this, who may not, what input must be rejected, what must never be exposed | Access to another owner's data; unexpected fields; error text revealing internals |
| Performance / limits | Behavior against a stated target | Key action speed, volume handled, behavior when slow | Response time; batch size; timeout message |
[book ch17, levels merged; table framing is addition]

Case category (what situation the check covers). Applies at any level.

| Category | Meaning |
|---|---|
| Normal | The expected, valid use |
| Edge | Unusual but possible situation |
| Failure | A situation where the system cannot complete the request safely |
[book ch17]

Mapping for non-service work: map levels to the project's equivalents (data checks, model evaluation, document review, hardware-in-loop). Keep the case categories unchanged. [addition]

Level labels used by the book in later chapters ("API", "UI", "AI workflow", "production smoke") are project-specific names for the same levels; record the project's mapping in the test spec header. [addition]

App G also lists "regression" as a test level. Treat regression as a purpose (re-running existing checks after a change), not an extra level: tag the check with its real level and mark it regression in the Scenario or Risk column. [book App G; resolution: addition]

## Edge-case questions
Start with the normal case, then ask what could be empty, too long, duplicated, expired, unavailable, unauthorized, or invalid. [book ch17]

| Question | Example |
|---|---|
| What if the value is empty? | A required title is blank |
| What if the value is too long? | A name far over the allowed length |
| What if the user or consumer is not allowed? | A non-owner edits an owner-only setting |
| What if the dependency fails? | A notification service cannot send |
| What if the action is repeated? | The submit action is triggered twice |
[book ch17, wording neutralized]

## Unit-level plan shape
A unit plan names the rule, the input, the expected output, and why the rule matters. [book ch17]
```
UNIT TEST PLAN: [rule name]
Requirement ID: [REQ-###]
Rule: [the rule]
Normal case: [input] should be accepted.
Edge case: [input] should be rejected or normalized ONLY if the spec allows it.
Failure case: [input] should be rejected with a clear validation error.
```
[book ch17; ID neutralized]

## Integration points to verify
| Integration point | Verify |
|---|---|
| Interface + persisted data | A valid request creates the right record and returns the correct response |
| Identity + action | Only an identified user or consumer can perform the action |
| Validation + response handling | Invalid input returns a clear error without creating bad data |
| Service + external dependency | Success, failure, and timeout are all handled |
[book ch17, neutralized]

## Security and abuse checks
Ask: who is allowed to do this, who is not, what input must be rejected, what information must never be exposed. [book ch17]

| Risk | Question |
|---|---|
| Unauthorized access | What happens when a caller reaches data they do not own? |
| Broken validation | What happens with unexpected fields or dangerous input? |
| Information leakage | Does an error reveal private data or system details? |
| Weak authorization | Can a normal caller perform an admin-only action? |
[book ch17]

Add SEC-### rule IDs to checks that prove a security rule, so a rule with no check is visible. [addition]

## Isolation-boundary checks
Applies only if the system has more than one owner, user group, project, or organization. Delete otherwise. [addition]
- Caller A cannot read, list, change, or export data owned by B (including by guessing or changing an identifier).
- A caller without the required permission is refused on the server side; hiding a control is not enforcement.
- Refusals do not reveal whether the other owner's data exists.
- Every query or operation that touches owned data is covered by at least one cross-boundary check.
[addition; the "cannot access another owner's data by guessing an ID" and "server-side enforcement" ideas appear in book ch25/ch27]

## Performance and limit checks
Start with a plain-language target: how fast the key action should feel, how many records it should handle, what happens when the system becomes slow. [book ch17] The project sets and records the numbers; this skill supplies none. [addition]

## Decisions to record (do not guess)
- Validation vs authorization order: the book does not specify the order. Treat it as a decision: record it as an ADR or spec note, test that order, and test that a refusal does not leak information. [addition]
- Normalization (for example case-insensitive values): allowed only if the spec says so; the book leaves the default to the spec. [book ch17]
- Whether a failed lookup (for example unknown account, missing file path, unknown record) returns a generic message (no existence leak) or a specific one: record the message wording policy. [addition]

## Test spec fields
Use `templates/test-spec.md`. Fields: [book ch17 and App G, adapted]

| Field | What to write |
|---|---|
| TEST-### | Stable ID [addition] |
| Requirement / AC | The REQ-### and AC-### being proved |
| Scenario | The behavior or situation verified |
| Level | Unit, integration, end-to-end flow, security/abuse, performance/limits |
| Case category | Normal, edge, failure [addition: separated from level] |
| Preconditions | What must be true first |
| Input / action | What the user, system, or test does; define valid, invalid, boundary, or malicious input [book App G] |
| Expected result | The result that proves correct behavior |
| Risk covered | The problem this protects against |
| Failure meaning | What it means if this test fails [book App G] |
| Plan state | Planned, Written, Automated, Manual, Blocked (replaces the book's "Automation status": manual, planned, automated, blocked) [book App G, adapted] |
| Result | Not run, Pass, Fail [addition] |
| Owner | Person or role responsible for maintaining the test [book App G] |
| Evidence | Where the proof lives (test name, log, report) [addition] |

## Generating tests with AI
Required prompt inputs (all four): the requirement, the acceptance criteria, the target test level, the expected output format. "Write tests" leaves too much room for guessing. [book ch18]

Prompt: generate tests from acceptance criteria [book ch18]
```
You are helping me generate tests from a software requirement. Do not invent behavior outside
the specification.
Requirement ID: [REQ-###]
Requirement: [paste the requirement]
Acceptance Criteria:
[paste acceptance criteria]
Test Level: [one of the project's levels]
Output Format:
- Test ID (TEST-###) and test name
- Requirement / AC covered
- Scenario
- Input
- Expected result
- Notes about edge cases
Generate only tests that directly prove the acceptance criteria.
```
(Adapted: the "Test ID (TEST-###)" and "Requirement / AC covered" output items are additions, and "Test Level" uses the project's levels instead of the book's list of unit/API/integration/UI. The book's review table asks for the ID beside each test.)

Prompt: plan before code [book ch17]
```
Using the requirement below, list the normal case, edge cases, and failure cases.
Do not write implementation code yet.
Requirement: [paste requirement]
Return the answer as a test planning table with: case type, input, expected result, and risk covered.
```

Prompt: turn a requirement into a test spec [book ch17]
```
You are helping me plan tests before code generation.
Requirement ID: [REQ-###]
Requirement: [paste requirement]
Create a test specification table with:
- normal case
- edge cases
- failure cases
- security cases
- expected result for each case
Do not write implementation code yet.
```

Prompt: tests from an interface contract [book ch18]
```
Use the following interface contract to generate test cases. Do not add operations or fields that are
not listed.
Operation: [name or entry point]
Purpose: [what it does]
Input: [fields and validation rules]
Success result: [outcome and shape]
Error results: [outcomes and shapes]
Side effects: [persisted data / messages / sessions / files]
Return a table with test name, input, expected outcome, expected result shape, and side effects to verify.
```
For an interface test, check: operation and input, outcome, result shape, validation rules, side effects. The contract in the spec is the source of truth. [book ch18]

Prompt: improve shallow tests [book ch18]
```
Review the following test cases against the requirement and acceptance criteria.
Requirement: [paste requirement]
Acceptance Criteria: [paste criteria]
Current Tests: [paste tests]
Identify:
1. Tests that are too shallow
2. Missing success cases
3. Missing failure cases
4. Weak assertions
5. Any behavior that is not supported by the spec
Then rewrite the tests so each one proves a specific acceptance criterion.
```

Specific generation targets [book ch18]
- Business rules: ask for allowed, blocked, and boundary cases plus the expected proof (for example "no record created for the blocked case"). Ask for invalid combinations and rule conflicts, not only success.
- Errors: ask for exact expected messages, outcome codes, and recovery behavior. Typical rows: missing required input; invalid format; wrong credentials (fails without creating state); unauthorized access (error plus hidden private data); temporary dependency failure (retry or report without corrupting data).
- Interface tests: check the operation, input, status or outcome, result body or shape, validation rules, and side effects. [book ch18] Grouping these as outcome, message/shape, and absence of unwanted side effects is an [addition].
- Screen or flow tests: focus on important flows, not tiny visual detail; give the screen, action, and visible result so the agent does not invent controls, labels, or flows.

## Reviewing generated tests
Label every generated test (and every gap) with one of four labels. [book ch18 exercise]

| Label | Meaning [addition] | Action [addition] |
|---|---|---|
| Strong | Proves a specific AC with a precise assertion | Keep; add the REQ/AC ID beside it |
| Weak | Runs but asserts vaguely or checks only that something happened | Rewrite to an exact result |
| Missing | A needed case (success, failure, validation, edge, security) has no test | Add it to the plan, then generate |
| Invented | Tests behavior not in the spec | Remove it, or update the spec first through a change record (CHG-###) |

Review table [book ch18]

| Area | Question | Fix |
|---|---|---|
| Requirement link | Does this prove a specific requirement or AC? | Add the requirement ID beside the test |
| Clear assertion | Does it check a real expected result? | Replace vague checks with exact outcomes, messages, values, or state changes |
| Failure path | Does it cover what happens when something goes wrong? | Add invalid input, missing data, permission failure, or timeout cases |
| No invented behavior | Did the agent add behavior not in the spec? | Remove it or update the spec first |
| Stable data | Can it run repeatedly with predictable results? | Use controlled test data; reset state when needed |

Decision rule for "remove or update the spec" (the book gives none): if the behavior is wanted, raise a CHG-### and get approval before the test is kept; if nobody asked for it, delete the test. [addition]

Chapter checklist for generated tests [book ch18]
1. The agent received the requirement and acceptance criteria.
2. Every generated test connects to a specific behavior.
3. Invented behavior was removed.
4. Vague assertions were strengthened.
5. Success, failure, validation, and edge cases are all present.
6. Business rules and error handling are checked.
7. Interface request and result contracts were confirmed.
8. Screen or flow tests stay on important flows.

Run the generated tests against the (not yet written or unchanged) code where practical: a new test that cannot fail proves nothing. [addition]

## QA practice [addition]
This whole section is an addition; the book does not cover it.

Exploratory and manual checks
- Use when no automated check can express the risk (look and feel, unclear wording, odd sequences) or to hunt for cases the plan missed.
- Timebox a session, state a charter (area, goal), note what was tried and what was seen. Convert each finding into a TEST-### row or a BUG-###.
- Manual checks still need a REQ/AC link and an expected result; mark Level as the same taxonomy and set Plan state to Manual.

Test evidence
- For each check keep: ID, date, build or version identifier, result, and where the proof is (report path, log, screenshot description).
- Pass without evidence is Not run. Approve features on evidence, not appearance. [book ch17 spirit]
- Record the run state of the whole plan: counts per Plan state (Planned, Written, Automated, Manual, Blocked) and per Result (Not run, Pass, Fail).

Defect triage
- Log each failure as BUG-### with the failing TEST-###, REQ, severity/priority (see file 07 scale), and reproduction steps.
- Triage order: security and data loss first, then requirement-breaking, then rest. Decide: fix now, defer with owner and date, or reject as spec gap (then raise CHG-###).
- A requirement with no test, no task, or no review evidence is not ready for production. [book ch28]

## Common mistakes
- Asking the agent to "write tests" with no acceptance criteria.
- Only happy-path tests. [book ch26]
- Planning tests after the code exists, so they describe the code.
- Counting test volume instead of requirement coverage.
- Keeping an invented test because it passes.
- Treating a performance number from an example as a project target.
- End-to-end checks used for every small rule.

Book inconsistencies resolved here are listed in docs/resolved/06-testing.md.

## Role notes [addition]
- Requester/PM: supplies acceptance criteria and priorities; confirms the edge and failure cases reflect real needs; decides remove-or-change for invented behavior.
- Builder: plans checks first, prompts the agent with all four inputs, fixes weak or missing tests, keeps IDs linked.
- Reviewer/QA: labels generated tests strong/weak/missing/invented, runs exploratory sessions, owns test evidence and defect triage.
