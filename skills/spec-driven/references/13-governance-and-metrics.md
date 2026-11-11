# 13 - Governance and Metrics (optional audit mode)

**Purpose:** Keep specs versioned and aligned with code, measure the engineering system, and make the process repeatable and adoptable.

**When to use:** After the first feature ships; on a recurring review (monthly or per release); when spec drift is suspected; when introducing the process to a team. Optional for very small projects.

**Inputs:** Current specs and their versions; change log; review comments; defect and incident records; feedback register.

**Output artifact:** Change log entries (CHG-###); `templates/quality-review.md` completed; improved templates and agent rules.

**Gate (done-when):** Repeatable-system checklist (section 7) answered; every behavior change has a CHG-### entry; review action items assigned. [addition]

Principle: "The goal is not to use AI more. The goal is to build better software with clearer intent, stronger tests, safer review, and a process you can repeat on any project." [book ch 30] "Do not design the process around one tool. Design it around artifacts, decisions, tests, and review evidence." [book ch 30] The process should be lightweight, visible, and strict where failure is expensive. [book ch 30]

## 1. Stages and artifacts (reference) [book ch 30]
| Stage | Artifact | Gate |
|---|---|---|
| Idea intake | Intent doc | Problem, users, constraints, non-goals clear |
| Requirements | Requirements + acceptance criteria | Each requirement testable or rejectable |
| Design | Technical spec, ADRs | Choices explicit and justified |
| Tasking | Agent task plan | Tasks small, bounded, reviewable |
| Testing | Test specification | Behavior, failures, edge cases covered |
| Implementation | Code and test changes | Matches specs; no unrelated changes |
| Review | Checklist + traceability evidence | Checked against requirements, architecture, security, tests |
| Release and learning | Deploy notes, monitoring, updated specs | Production feedback becomes spec update |

Use the canonical layout in SKILL.md (docs/, docs/decisions/, docs/changes/, docs/feedback/, tasks/). Feedback register: `docs/feedback/`; change records: `docs/changes/`; agent rules: AGENTS.md/CLAUDE.md. [addition]

## 2. Spec versioning
Code should not quietly move ahead of the specification. When behavior changes, the requirement, test, task, and review evidence change with it. All that is needed: a clear version label, a change log, and a decision owner. Works without any external platform. [book ch 30]

| Item | Version example | Changes when | Approver | Evidence |
|---|---|---|---|---|
| Requirements | v1.2 | New requirement, changed priority, clarified non-goal | Requester | Change note + affected REQ IDs |
| Technical spec | v1.4 | Architecture, interface, data, integration decision | Technical lead/Reviewer | ADR or design note |
| Test spec | v1.3 | New behavior, bug fix, edge case, failure path | Builder + Reviewer | New/updated tests |
| Agent rules | v1.1 | Repeated agent mistake, new boundary | Team lead | Reason + example |
| Release plan | v1.0 | Target, rollback, monitoring rule | Release owner | Checklist update |

[book ch 30, version labels shortened].

### Change records (CHG-###)
Use `templates/spec-change-record.md` (single format and statuses). [book ch 30]

Maintenance Rule: when behavior changes, update the spec. Update triggers [book ch 3]: new user behavior -> requirements and product specification; data field or relationship change -> technical spec and data model; new security rule -> requirements, technical spec, test plan; bug reveals missing behavior -> requirement, test plan, task history; deployment change -> deployment checklist and maintenance notes.

## 3. Traceability
Every accepted code change points to a requirement and a test. Every requirement points forward to at least one task and one reviewable proof. [book ch 30]

| REQ | TASK | TEST | Code area | Review evidence | Status |
|---|---|---|---|---|---|
| REQ-[###] | TASK-[###] | TEST-[###] | [area] | [note] | Needs review / Approved / Needs update |

Status = Review status vocabulary. A requirement with no test, task, or review evidence is not ready for production. [book ch 28]

## 4. Template library and agent rules
- After every project ask: which template should be improved so the same confusion does not happen again? [book ch 30] Templates do not replace thinking; they ensure it is not skipped. [book ch 30]
- Template improvement signals: fewer basic scope questions (intent); tests map clearly to requirements; agent changes fewer unrelated files (task brief); bugs caught before release (test spec); evidence-based review (checklist); calmer deploys (release checklist). [book ch 30]
- When a mistake repeats, improve the system, not only the bug. [book ch 30]

Minimum sections per template [book ch 30]:

| Template | Minimum sections |
|---|---|
| Intent | Problem, users, value, constraints, non-goals, success |
| PRD | Requirements, acceptance criteria, priorities, risks |
| Technical spec | Architecture, data, interfaces, integrations, errors, security |
| Agent task brief | Task, context, constraints, files, tests, expected output |
| Test specification | Unit, integration, end-to-end, failure, security, performance |
| Review checklist | Requirements, design, security, tests, maintainability |
| Release checklist | Config, migration, rollback, monitoring, owner |

Agent rule areas and the review evidence for each [book ch 30]:

| Area | Rule example | Review evidence |
|---|---|---|
| Scope control | Do not modify files outside the task scope unless asked first | File-change summary |
| Code style | Clear names, small functions, predictable module boundaries | Reviewer readability check |
| Error handling | Safe user messages; technical details logged internally | Error-path tests |
| Security | Never hard-code secrets or bypass authorization checks | Security review checklist |
| Testing | Every behavior change includes relevant tests | Passing test list and coverage notes |
| Explanation | Summarize assumptions, tradeoffs, files changed | Agent completion note |

Reusable agent rules [book ch 30]:
```
General behavior:
- Follow the linked requirement and technical specification.
- Ask questions before filling major gaps.
- Do not expand scope without approval.
Coding standards:
- Keep functions small and readable.
- Use clear names and simple control flow.
- Add comments only where they explain non-obvious decisions.
Security rules:
- Do not hard-code secrets.
- Do not weaken authentication or authorization.
- Validate all external input.
Testing rules:
- Add or update tests for every behavior change.
- Include success paths, failure paths, and edge cases.
Completion note:
- List files changed.
- List assumptions.
- List tests added or updated.
- List remaining risks or questions.
```
Keep it short and add a rule only after a real repeated mistake, with the reason and an example. [addition, from the versioning evidence column]

## 5. Quality metrics
Measure the engineering system, not the amount of AI usage. [book ch 30] Directions only; the book gives no numeric targets, so the project decides its own and records them. [addition]

| Metric | Shows | Measure simply | Healthy direction |
|---|---|---|---|
| Requirement clarity | Specs usable before coding | Count questions raised at task handoff | Fewer basic questions over time |
| Rework rate | Generated work needing heavy correction | Tasks reopened after review | Lower reopen rate |
| Test usefulness | Tests catch real problems | Bugs found by tests before release | More meaningful failures before production |
| Review findings | Where agent or humans miss issues | Tag review comments by category | Repeated categories decrease |
| Spec drift | Code and specs aligned | Released behavior reflected in docs? | Fewer undocumented behavior changes |
| Production stability | Releases behave reliably | Errors, incidents, rollbacks, response time | Fewer incidents, faster recovery |

Caveat: question count is a proxy for clarity only. [book ch 30 gap, addition] On a small team, start with a few metrics you can collect cheaply. [addition]

### Monthly quality review
Use `templates/quality-review.md`. Fields: period; shipped; changed requirements; tasks done with agent support; defects before and after release; most common review issue; most common agent mistake; template and agent rule needing improvement; drift found; actions. [book ch 30]
Review rhythm detail is [addition]: run it monthly or per release; assign each action an owner and update the affected template or rule before the next cycle.

## 6. Adoption path [book ch 30]
Start with one project, one small template set, one review habit.

| Step | Do | Avoid | Proof |
|---|---|---|---|
| Start small | Use on one feature/project | Forcing a heavy process everywhere | One feature moves spec to tested code cleanly |
| Create examples | Show completed specs, tasks, tests, reviews | Blank templates without guidance | People copy good examples |
| Make review visible | Review agent output against specs in the open | Accepting output privately without evidence | Comments become more specific |
| Improve templates | Update after real use | Treating templates as permanent | Repeated confusion decreases |
| Train through work | Teach during real delivery | Abstract training with no project | People use it unprompted |

Adoption Rule: a team adopts the system when it makes work clearer and safer. If it feels like paperwork, simplify until it protects quality without slowing useful progress. [book ch 30]

## 7. Final rules [book ch 30]
Use AI inside engineering discipline; do not ask it to replace it.
1. Begin every project with engineering intent before asking for code.
2. Turn intent into requirements that can be tested.
3. Write technical specifications before implementation decisions become scattered.
4. Give agents bounded tasks with context, constraints, and expected outputs.
5. Plan tests before generation so the agent builds toward clear proof.
6. Review generated code against requirements, architecture, security, and maintainability.
7. Deploy with rollback, monitoring, and maintenance in mind.
8. Treat production feedback as part of the specification lifecycle.
9. Keep improving templates, rules, and checklists after each project.

### Repeatable-system checklist [book ch 30]
- [ ] Process: can you explain the path from idea to production review?
- [ ] Documentation: can a new human or agent find the current source of truth?
- [ ] Versioning: are requirement and spec changes named, dated, explained?
- [ ] Templates: do projects reuse proven briefs, specs, test plans, review checklists?
- [ ] Agent rules: do agents receive constraints, coding standards, completion rules?
- [ ] Traceability: can each feature be traced requirement -> task -> test -> code -> review -> release?
- [ ] Quality metrics: do you measure defects, rework, review findings, drift, stability?
- [ ] Adoption: does the workflow reduce confusion and give better evidence?

## Common mistakes
- Updating code without a CHG entry, so drift accumulates silently.
- Tracking AI usage volume instead of quality outcomes.
- Setting numeric targets by copying another project.
- Freezing templates after the first use.
- Introducing the whole process on every team at once.

Book inconsistencies resolved here are listed in docs/resolved/10-14.md.

## Role notes [addition]
- Requester: approves requirement versions; decides scope changes; reads the monthly review.
- Builder: writes change-log entries and updates tests/tasks with each change; keeps agent rules current.
- Reviewer/QA: tags review findings, checks traceability and drift; prepares the metrics.
- Approver: approves spec and release versions; assigns owners to action items.
