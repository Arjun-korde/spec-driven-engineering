# 11 - Roles and Handoffs

**Purpose:** Define who does what at each stage, how work is handed between roles (and to the agent), how feedback and scope changes are controlled, and how a solo builder covers all roles.

**When to use:** Any time more than one person or the agent touches the work; at every handoff; at weekly alignment; when feedback or a scope change arrives. Solo builders use the solo-mode section.

**Inputs:** Current stage and artifact; source-of-truth location; list of people (or "me") holding each role.

**Output artifact:** Completed handoff (`templates/handoff.md`), feedback records (`templates/feedback-record.md`), updated spec + change log after decisions.

**Gate (done-when):** [addition, derived from the book's Team Workflow Checklist, ch 29]
- [ ] The team agrees where requirements, specs, tasks, tests, decisions and feedback live (one source of truth).
- [ ] Each feature has a handoff with problem, users, acceptance criteria, risks, non-goals.
- [ ] Each agent task has scope, context, constraints, expected output, review rules.
- [ ] AI output is reviewed against requirements, architecture, security, tests, maintainability.
- [ ] Every feedback item has affected artifact, owner, decision, status.
- [ ] Accepted scope changes updated requirement, design, tests, tasks, traceability.
- [ ] A regular alignment rhythm exists.

---

## 1. Principles [book ch 29]
- "AI agents do not remove the need for teamwork. They increase the need for clear handoffs, shared context, explicit decisions, and disciplined review." [book ch 29]
- A shared spec answers three questions for everyone: What are we building? How will we know it works? What has changed since the last decision? [book ch 29]
- The shared spec is the working memory of the project: what is built, why, what is not built, how quality is checked, what changed after review. [book ch 29]
- **Agent Control Rule:** The agent never receives a task the team cannot review. If output cannot be checked against requirements, tests, or architecture, the task is too vague. [book ch 29]
- **Scope Control Rule:** A scope change is not accepted until the relevant requirement, design, tests, tasks, and review checklist are updated. Otherwise it is only a conversation, not a controlled decision. [book ch 29]

## 2. Roles by function
Roles are functions, not job titles. One person may hold several; none is required to exist. [addition]

| Role | Needs | Goes wrong without specs | Helpful artifact |
|---|---|---|---|
| Requester/PM | Clear scope, user needs, priorities, acceptance criteria | Features built that miss the goal | Requirements doc, change log [book ch 29] |
| Builder | Architecture, constraints, data, interface contracts, tests | Code works locally but breaks design, security, maintainability | Technical spec, task list [book ch 29] |
| Agent | Bounded context, explicit instructions, examples, forbidden changes | Guesses, overbuilds, changes unrelated code | Task brief / context pack [book ch 29] |
| Reviewer/QA | Requirements, expected behavior, tests, risks, evidence | Review becomes opinion instead of evidence | Review checklist, traceability matrix [book ch 29] |
| Approver | Visible progress, tradeoffs, decisions, impact | Feedback arrives late, causes rework | Decision log, demo notes, feedback register [book ch 29, "Stakeholder"] |

Each role sees a different angle: Requester = user value and priority; Builder = structure, constraints, maintainability; Reviewer = correctness and risk; Agent = only the context it receives. [book ch 29]

## 3. Merged owner / approver / helper table (per stage)
Merges the book's 8-step workflow, review layers, and versioning approvers. [addition: the merge; cell contents derived from book ch 29-30]

| Stage | Owner | Approver | Helpers | Artifact | Quality gate |
|---|---|---|---|---|---|
| 1 Clarify intent | Requester | Requester | Agent (clarifying questions) | Intent doc | Non-goals and risks stated |
| 2 Requirements | Requester + Builder | Requester | Reviewer, Agent (drafts) | Requirements with acceptance criteria | Each requirement testable |
| 3 Engineering plan | Builder | Builder or technical lead | Reviewer, Agent (critique) | Technical spec, tasks, tests, ADRs | Design matches scope and constraints |
| 4 Agent task brief | Builder | Builder | - | Bounded task brief | Enough context, clear limits |
| 5 Generate and review output | Agent + team (Builder, Reviewer) | Reviewer | QA | Draft code/tests/docs | Passes review checklist |
| 6 Capture feedback | Whole team | Owner named per item | Requester | Feedback records | Every item has owner and status |
| 7 Update specs | Assigned owner | Per artifact (see section 8) | Agent (refreshed context) | Updated requirements/tasks/tests/traceability | Source of truth reflects reality |
| 8 Release or iterate | Team lead / release owner | Approver | Reviewer | Accepted change or next cycle | No unresolved high-risk gaps |

Steps 1-8: [book ch 29]. Role names neutralized. Test planning happens at step 3, before implementation (see docs/resolved/10-14.md).

## 4. Handoff rules
Three directions; pick by "who am I handing to". [addition: grouping; templates from book ch 29]

| Direction | Use template | Must contain |
|---|---|---|
| Requester -> Builder | `templates/handoff.md` part A | Problem, users, goals, must-have requirements, acceptance criteria, non-goals, constraints, risks, open questions, decision owner, date [book ch 29] |
| Builder -> Agent | `templates/handoff.md` part B | Task boundary, relevant context, forbidden changes, expected output, review rules [book ch 29] |
| Reviewer -> Decision | `templates/handoff.md` part C | Findings by layer, evidence, decision |
| Builder -> QA (test-plan review, before code) | Use the block in section 4a (no template part; copy it into the review request) | Link to test-spec and traceability matrix, REQ/AC in scope, open decisions, blocked tests, review deadline [addition] |

Handoff item weaknesses to avoid [book ch 29]:

| Item | Weak form |
|---|---|
| Problem statement | Described only as a feature request |
| User requirement | No pass/fail condition |
| Priority and non-goals | No boundaries, so team overbuilds |
| Risk notes | Risks found after implementation |
| Open questions | Agent fills gaps with guesses |

Agent handoff elements [book ch 29]: one task boundary (not "improve the whole app"); relevant context (not "you know what I mean"); forbidden changes; expected output (not "do it well"); review rules. An agent has no hidden project memory; supply everything. "Do not proceed if:" has no sub-guidance in the book; fill it with the stop conditions for that task (e.g. a required answer is missing, a forbidden file would need to change). [addition]

Handoff checklist, Requester -> Builder [addition, derived from book ch 29 table]
- [ ] Problem is stated as pain/reason, not just a feature
- [ ] Every requirement has a pass/fail acceptance criterion
- [ ] Non-goals and priorities listed
- [ ] Risks and sensitive areas listed
- [ ] Open questions listed with a decision owner

Handoff checklist, Builder -> Agent [addition, derived from book ch 29 table]
- [ ] One bounded task, linked to requirement IDs
- [ ] Context supplied is the minimum needed and is current
- [ ] In-scope and out-of-scope files/areas named
- [ ] Expected output and tests defined
- [ ] The result can be reviewed against requirements, tests, or architecture (Agent Control Rule)

### 4a. Builder -> QA handoff and QA checklist [addition]
Handoff block (send before implementation starts):
```
Builder -> QA: please review the test plan for [feature]
Test spec: [docs/test-spec.md]   Mapping: [docs/traceability-matrix.md]
In scope: [REQ-### / AC-### list]
Open decisions that block tests (Plan state = Blocked): [list, with owner]
Please answer by: [date]. Reply with: Approved | Needs update + the missing AC/checks.
```
QA checklist: reviewing a test plan
- [ ] Every REQ in scope has a normal, an edge, and a failure check (or n/a with a reason).
- [ ] Every AC-### is verified by at least one TEST-###; no TEST verifies nothing.
- [ ] Each expected result is specific enough to pass or fail without interpretation.
- [ ] Failure paths, hostile input, and (where relevant) boundary checks are present.
- [ ] Blocked tests name the open decision and its owner.
- [ ] A missing AC found here is raised as a CHG-### (`templates/spec-change-record.md`), not patched silently.

What the Requester/PM decides at each stage:

| Stage | Decision to make | Read first |
|---|---|---|
| Intent | Problem, users, out of scope; answer open questions | `docs/intent.md` |
| Requirements | Priority (Must/Should/Could/Won't), AC wording, which assumptions to confirm | `docs/requirements.md` (Assumptions table) |
| Design and tasks | Trade-offs that change scope, cost, or timing | ADRs, task list |
| Tests | Accept the behavior the checks encode; decide on gaps raised as CHG | `docs/test-spec.md` coverage check |
| Release | Go/no-go, known gaps accepted | Review package, traceability gap check |

## 5. Reviewing agent output as a team [book ch 29]
Do not ask "does this look good"; ask whether it satisfies requirements, respects architecture, passes tests, protects users, keeps maintenance clear.

| Layer | Main question | Evidence | Who helps | Decision |
|---|---|---|---|---|
| Requirement fit | Solves the user need? | Requirement ID, acceptance criteria | Requester, Builder | Accept / revise |
| Architecture fit | Follows agreed design? | Technical spec, ADRs, module boundaries | Builder | Accept / refactor |
| Security and privacy | Exposes data or weakens controls? | Security checklist, permission tests | Builder, Reviewer | Block if unsafe |
| Test evidence | Tests prove behavior and failure paths? | Unit/integration/UI/edge tests | Builder, QA | Accept / add tests |
| Maintainability | Next person can understand? | Naming, useful comments, updated specs | Reviewer | Accept / simplify |

Decision vocabulary: use Accept | Revise | Block for all layers; "refactor", "add tests", "simplify" are the reason noted with Revise. [addition]

Prompt (to the agent, as reviewer) [book ch 29]:
```
Review this output against the linked requirement and technical specification.
Do not rewrite yet.
Report:
1. Requirement gaps
2. Architecture mismatches
3. Security or privacy concerns
4. Missing tests
5. Overbuilt or out-of-scope changes
6. Questions for the Requester
7. Safe refactoring suggestions
```

## 6. Feedback loop
Good feedback identifies the affected requirement, explains the gap, proposes a decision, and states who owns the next action. [book ch 29]

| Type | Record in | Owner | Next action |
|---|---|---|---|
| Clarification | Feedback register + requirements | Requester | Define meaning, update acceptance criteria |
| Bug | Issue list, test plan, security checklist | Builder | Fix and add regression test |
| Design concern | Behavior spec / rules | Requester + Builder | Add rule and test |
| Scope request | Scope change log (CHG-###) | Requester | Decide now / later / reject |
| Operational concern | Reliability spec, monitoring plan | Builder | Add logging / error tracking |

[book ch 29; owner names neutralized]. Use `templates/feedback-record.md`. Status values: New | Under review | Accepted | Rejected | Deferred. [book ch 29] Where records live is unspecified in the book; keep feedback records in `docs/feedback/` and change records in `docs/changes/`. [addition]

Loop: capture -> assign owner -> decide -> update spec and tests -> give agent refreshed context -> release or iterate. [book ch 29, steps 6-8]

## 7. Scope-change control
Scope changes are not bad; they are dangerous when they enter without a decision trail. Evaluate on value, risk, effort, timing, impact on requirements, design, tests, deployment. [book ch 29]

| Question | Decision options | Artifact to update |
|---|---|---|
| Supports the project goal? | Accept / reject / defer | Intent, requirements |
| Affects existing requirements? | Revise or create new | Requirements, traceability |
| Affects architecture? | Update design or ADR | Technical spec, ADRs |
| Affects release timing? | Move release, reduce scope, defer | Roadmap, task plan |
| Needs new tests? | Add tests before implementation | Test plan, checklist |

Then log it as CHG-### using `templates/spec-change-record.md`. The agent does not expand scope without approval. [book ch 30]

## 8. Approvers by artifact [book ch 30, neutralized]

| Artifact | Approver | Evidence |
|---|---|---|
| Requirements | Requester | Change note + affected REQ IDs |
| Technical spec | Technical lead or Reviewer | ADR or design note |
| Test spec | Builder + Reviewer | New/updated tests |
| Agent rules | Team lead | Reason + example |
| Release plan | Release owner | Checklist update |

## 9. Alignment rhythm [book ch 29]
Alignment is a rhythm, not a one-time meeting.

| Practice | When | Output |
|---|---|---|
| Spec review | Before implementation or after major change | Approved spec or action list |
| Task kickoff | Before assigning to Builder or Agent | Clear task brief |
| Mid-work checkpoint | Agent/Builder finds ambiguity | Decision or revised task |
| Review | After meaningful agent change | Accept / Revise / Block |
| Spec update review | After feedback or release learning | Updated source of truth |

Weekly alignment questions [book ch 29]:
```
1. What requirement changed this week?
2. What decision did we make that must be recorded?
3. Which agent outputs were accepted, revised, or rejected?
4. Which tests were added because of feedback?
5. Which task is too vague for an agent to execute safely?
6. What is now out of scope?
7. What must be updated before the next implementation cycle?
```

## 10. Solo-builder mode [addition]
One person holds every role. Roles stay separate as hats, not as skipped steps.
- Write the handoff to the agent anyway (part B); it is the context the agent otherwise lacks.
- Do requirements and review in separate sittings; review the output as Reviewer against the written criteria, not against memory.
- Approver hat: record decisions in the change log so the "owner" is you with a date.
- Use the weekly questions as a short self-check; skip practices that add paperwork without protecting quality (adoption rule: simplify until it protects quality without slowing useful progress). [book ch 30 principle]
- Pattern worth keeping: agent critiques design first, then drafts tests only (reviewed before code), then drafts code and notes assumptions. [book ch 29 worked example]

## 11. Team workflow pack (optional header block) [book ch 29]
Project; release goal; source-of-truth location; decision owner; requester; engineering owner; reviewer(s); agent role; current requirements; technical spec; active tasks; test plan; open questions; scope changes; feedback items; next review date; definition of done. "Product owner" in the book's pack = Requester here.

## Common mistakes
- Sending the agent a task nobody can review.
- Accepting a scope change in conversation without updating tests and traceability.
- Handoff with open questions left silent so the agent guesses.
- Review by opinion with no requirement ID or test evidence.
- Feedback with no owner or status.

Book inconsistencies resolved here are listed in docs/resolved/10-14.md.

## Role notes [addition]
- Requester: owns intent, priorities, acceptance criteria, scope decisions, answers open questions.
- Builder: converts intent to design and bounded tasks; owns agent handoffs and bug fixes.
- Reviewer/QA: judges output by layer with evidence; signs traceability.
- Approver: approves spec versions and releases; keeps decision trail.
