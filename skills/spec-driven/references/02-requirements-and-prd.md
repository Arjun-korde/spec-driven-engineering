# 02 - Requirements and Product Spec (PRD)

**Purpose:** Convert the Engineering Intent into testable requirements, then into a product spec (PRD) that fixes users, scope, flows, priorities, and success metrics, before any technical design. [book ch5, ch6]
**When to use:** After the intent gate passes. Requirements always; PRD when there are user-facing flows or scope to negotiate. For a small change, a few REQ rows plus acceptance criteria may be enough. [addition]
**Inputs:** `docs/intent.md`.
**Output artifacts:** `docs/requirements.md` (`templates/requirements.md`); `docs/prd.md` (`templates/prd.md`). PRD = Product Requirements Document; the book also calls it the "product specification" or product spec (ch4 file `product-spec.md`); same artifact. [book ch1, ch4, ch6]
**Gates:** see "Gate A" and "Gate B".

## Part A - Requirements

### Core rules
- A useful requirement is clear, testable, bounded, traceable. "If you cannot test it, limit it, or connect it to later work, it is not ready for AI-assisted development." [book ch5]
- Testability is the first quality filter. If you cannot test a requirement, rewrite it. [book ch1]
- No implementation in requirements (no database, framework, deployment, UI-library choice). Those go in the technical spec. [book ch3, ch5]
- Do not write impossible quality claims ("never fail", "always fast"). Use measurable expectations, known limits, and graceful failure behavior. [book ch5]
- Safest habit: "Could two people interpret this differently?" If yes, rewrite. [book ch5]
- Write each requirement as if a careful builder, tester, or agent will ask: who is this for, what exactly must happen, how will I know it works, what is out of scope. [book ch5]

### Weak vs stronger wording
| Weak | Stronger | Why |
|---|---|---|
| The app should be easy to use. | A new user must be able to create an account in N steps or fewer. | Observable behavior. |
| Users should manage tasks. | A team member must be able to create, edit, complete, and delete their own tasks. | Actor and actions named. |
| The system should be fast. | The [operation] must finish within [limit] for up to [workload size]. | Measurable. |

[book ch5; numbers replaced by placeholders: the project sets and records its own values] [addition]

### Requirement structure
Actor + Action + Object + Rule + Acceptance criterion. [book ch1]
Format: `REQ-###: [Actor] must be able to [action] [object] so that [outcome].` [book ch5]
Actor may be a person, calling system, job, or consumer of a library or CLI. [addition]

| Element | Question |
|---|---|
| Actor | Who performs the action? |
| Action | What must they do? |
| Object | What is acted on? |
| Result | What happens afterward? |

[book ch5]

### Requirement types (use a Type column, not ID prefixes)
IDs are `REQ-###`; a `Type` column holds functional | nonfunctional | business-rule | constraint. The book used REQ-F / REQ-NF / REQ-R / BR / CON prefixes in chapters and plain REQ-001 in Appendix B. [book ch5, App B; unification addition]

- **Functional:** what the system does (actor, action, object, result). [book ch5]
- **Nonfunctional:** how well: performance, security, reliability, accessibility, usability, privacy, maintainability. State early so the agent does not build for the easiest case only. [book ch5]
- **Roles and permissions:** define before design as a table: role / can do / cannot do. Need not be complex. Roles may be consumer/operator/maintainer or none, depending on the project. [book ch5; generalization addition]
- **Business rules:** policy decisions the software enforces. Write separately from code instructions. When a rule changes, update spec first, then tests, then code. [book ch5] For each interface, also list the business rules enforced beyond basic validation (field "Business rules" in the interface contract). [book App D]
- **Constraints:** fixed limits: technology, time, data, environment, integration, compliance, team skill, budget. State real limits because agents invent ideal solutions. A constraint guides the solution; it must not lower quality standards. [book ch5]

| Nonfunctional category | Example shape |
|---|---|
| Performance | [operation] completes within [limit] at [workload]. |
| Security | Only [authenticated/authorized actor] may access [data]. |
| Reliability | If [operation] fails, the system shows an error and preserves the user's input. |
| Usability | A new user can complete [first task] without a separate manual. |
| Maintainability | [Concern A] logic is separated from [concern B] logic. |

[book ch5, generalized] Illustrations in other stacks: web API (latency per request), CLI (exit code and message on bad flag), library (exception type on invalid argument), data pipeline (job re-runnable without duplicate output), LLM feature (fallback answer when retrieval returns nothing). [addition]

### Missing-state rule
Every requirement states behavior for the non-happy cases that apply: missing input, empty result, delayed or timed-out response, denied access, and failed dependency or operation. If a case does not apply, write "n/a" with a reason rather than leaving it silent. Each such case gets its own acceptance criterion. [addition, generalized from the ch27 dashboard quality rule: "Every dashboard requirement must say what happens when data is missing, delayed, filtered to zero, or blocked by permission."]

### Acceptance criteria (Given / When / Then)
```
AC-001: Given [starting condition], When [action or event], Then [expected result].
AC-002: Given [edge or failure condition], When [action occurs], Then [safe behavior or error response].
```
[book ch5] Link each AC to its REQ (column or nesting). [addition] Minimum per feature before building: at least one normal, one edge, one failure check. [book ch17] The book says "every important requirement" in one place and "each requirement" in another; use: every requirement has at least one AC. [addition]

Reopening an approved requirements artifact: if a later stage (usually test planning, see 06) exposes a missing AC or REQ, do not patch silently. Record a CHG-### (`templates/spec-change-record.md`), add the AC, and re-run Gate A for that item only. [addition]

Assumptions vs open decisions: defined in `01-intent.md` ("Assumption vs open decision"); record assumptions in the Assumptions table of the requirements template. [addition]

### Common mistakes
| Mistake | Weak | Fix |
|---|---|---|
| Vague wording | The dashboard should be nice. | State what it shows and how it is used. |
| No actor | Tasks can be deleted. | Name the role and the condition. |
| No boundary | Users can manage projects. | List allowed actions per role. |
| No acceptance criteria | Users can reset passwords. | Add the flow, expiry, failure behavior. |
| Implementation leak | Use a modal with framework state. | Describe behavior; defer to technical spec. |

[book ch5]

### Workflow
1. Start from the intent's problem statement. 2. List user roles before features. 3. Write functional requirements (actor, action, object, outcome). 4. Add nonfunctional requirements. 5. Write business rules apart from implementation choices. 6. List constraints. 7. Add acceptance criteria. 8. Review for ambiguity before the PRD. [book ch5]

### Requirement quality checklist
| Check | Question |
|---|---|
| Clear | Understandable without guessing? |
| Actor defined | Who performs the action? |
| Action defined | Exactly what must happen? |
| Bounded | Avoids hidden extra features? |
| Testable | Can you prove whether it works? |
| Traceable | Can it become a task, test, and code change? |
| No implementation leak | Avoids decisions that belong in the technical spec? |
| Missing states [addition] | Missing/empty/denied/failed behavior stated or marked n/a? |

[book ch5]

### Gate A - move to PRD or technical design when
- [ ] Every requirement passes the quality checklist above. [book ch5]
- [ ] Roles/permissions table exists (or "no roles" stated). [book ch5]
- [ ] Every requirement has at least one AC, including a failure AC. [book ch5; failure check minimum from ch17]
- [ ] Open questions that affect design are answered or owned. [book ch5]
- [ ] Approver marked the file Approved. [addition]

### Prompts (Part A)
Wording is neutralized from the book (e.g. "software" or "user" dropped where the sense is unchanged). [book ch3, ch5]

**P2.1 Draft requirements from intent** [book ch3]
```
Use this Engineering Intent to draft clear software requirements.
Intent: [paste intent]
Return functional requirements, nonfunctional requirements, business rules, permissions,
constraints, and acceptance criteria. For each requirement include behavior for missing,
empty, denied, and failed cases. Do not choose implementation tools yet.
```
(The sentence "For each requirement include behavior for missing, empty, denied, and failed cases" is added.) [addition]

**P2.2 Extract business rules** [book ch5]
```
You are helping me prepare requirements.
Project idea: [paste the project idea]
List the business rules that the system should enforce.
Do not write code.
For each rule, explain what behavior it controls and what could go wrong if the rule is missing.
```

**P2.3 Requirements review** [book ch5]
```
Act as a strict requirements reviewer.
Review the requirements below for ambiguity, missing actors, missing acceptance criteria,
hidden implementation details, and untestable language.
Return your response in three sections:
1. Problems found
2. Revised requirements
3. Questions that must be answered before design begins
Requirements:
[paste your requirements here]
```
Optionally add "and missing behavior for empty, denied, or failed cases". [addition]

**P2.6 Turn product goals into dashboard requirements** (UI/analytics projects only) [book ch27, neutralized]
```
You are assisting with a [dashboard / analytics / reporting] feature.
Use the product goals below to produce measurable dashboard requirements.
For each requirement, include: requirement ID, user role, metric or action, acceptance criteria,
data source, and failure condition. For each, also state what happens when data is missing,
delayed, filtered to zero, or blocked by permission.
Product goals:
1. [goal]
2. [goal]
3. [goal]
```
(The book's example goals are "Show account health at a glance", "Reveal product usage patterns", "Support action after insight". The book's dashboard requirement table has columns: ID, requirement, role, acceptance criteria, failure/empty state. The last sentence of the prompt is added from the ch27 quality rule.) [book ch27; last sentence addition]

## Part B - Product spec (PRD)

### Core rules
- The PRD is the product agreement: goal, users, use cases, scope, stories, flows, priorities, metrics in one place. It does not replace technical design. [book ch6]
- It answers: why (goal), who (users), what is included, what is excluded, how users experience it, how you will know it worked. [book ch6]
- Do not put persisted-data layout, frameworks, interface endpoints, or file structure in the PRD. [book ch6]
- Stage gate: do not move into technical design until the main user flows and first-version scope are clear. [book ch3]
- For libraries, infrastructure, data/ML, or internal tools, read "persona" as consumer/stakeholder and "user flow" as interaction or process flow. [addition]

### Goal and success metrics
Goal format: `[This product helps] [target user] [achieve outcome] [under important constraint or context].` [book ch6]
Metrics: (1) a measurable user or business result, (2) a quality or adoption signal, (3) a failure or risk signal to monitor. Two or three, observable or measurable. [book ch6] The project sets its own target numbers and records how they are measured. [addition]

| Weak goal | Stronger goal |
|---|---|
| Build a task app. | Help small teams create, assign, and track daily work in one place. |
| Make customer support better. | Help support teams answer repeated questions faster with an approved knowledge base (book: "AI-assisted knowledge base"). |
| Create an analytics dashboard. | Help managers see key business numbers without opening spreadsheets. |

[book ch6]

### Personas and use cases
Persona = who; use case = what they need to do. Fields: name or role; goal; frustration; main use cases; success condition. [book ch6]

### Scope and out of scope
- Every feature costs time, attention, testing, maintenance. For each included feature write one sentence on why it belongs in this version; if you cannot, move it to later. [book ch6]
- Out of scope protects focus and stops the agent adding unapproved features. Mark each item: permanently excluded | delayed | waiting for information, with a reason. [book ch6]

| In scope: feature | Behavior | Why now |
|---|---|---|
| [feature] | [what the actor can do] | [value] |

| Out of scope item | Reason | Future status |
|---|---|---|
| [item] | [reason] | permanent / delayed / waiting for info |

[book ch6]

### User stories
`US-###: As a [specific role], I want [one clear capability], so that [specific benefit].` One user outcome each; not too large. Link: supports REQ-###; later TASK-###, TEST-###. [book ch6]
Weak: "As a user, I want tasks." Stronger: "As a team member, I want to create a task with a due date so that I can record work that needs attention." [book ch6]

### User flows
Steps: start, action, input, system response, success path, at least one failure path. A written flow is enough. [book ch6] Include the missing-state cases (empty list, denied access, failed save) as additional paths. [addition] Each flow failure path should match an AC in requirements.md. [addition]

### Priorities (MoSCoW)
| Priority | Meaning | How to use it |
|---|---|---|
| Must | The first useful version fails without it; required for the first usable release. [book ch6, App B] | Do not start implementation until this is clear. [book App B] |
| Should | Important, but the product can still be tested without it; not release-blocking. [book ch6, App B] | Plan after Must requirements. [book App B] |
| Could | Useful if time allows. [book ch6, App B] | Keep for later unless capacity remains. [book App B] |
| Won't | Not needed for the first version; not included in this release. [book ch6 "Later", App B "Won't"] | Protects scope from uncontrolled growth. [book App B] List it under out of scope. [addition] |

Test: if the feature is missing, can you still test the main product idea? If yes, it may not be a Must. Reason: the agent generates code fast, so speed becomes dangerous if low-value features are built before the core works. [book ch6] Chapter 6 names the fourth label "Later"; Appendix B names it "Won't"; this skill uses Won't. Record priority per REQ in requirements.md or per feature in the PRD. [book ch6, App B]

PRD fields in Appendix B also include non-goals, dependencies (services, data sources, interfaces, teams, approvals), and risks (product, technical, security, operational); the template carries them as optional sections. A requirement entry may also carry Title, Description, User Value, Dependencies, and Notes. [book App B]

### PRD workflow
1. Start from requirements. 2. Short product summary. 3. One main goal before features. 4. Two or three success metrics. 5. Personas and use cases. 6. In scope vs out of scope. 7. Stories for the key outcomes. 8. Flows for Must features. 9. Prioritize before the technical spec. [book ch6]

### PRD quality checklist
| Check | Question |
|---|---|
| Clear product goal | Can you state the outcome in one sentence? |
| Known users | Primary users and goals identified? |
| Useful success metrics | Can you tell whether it is working? |
| Controlled scope | States what is included now? |
| Protected focus | States what is out of scope? |
| User stories | Key features written from the user point of view? |
| User flows | Can you follow the path from start to success or failure? |
| Ready for technical spec | Can a technical designer proceed without guessing product direction? |

[book ch6]

### Gate B - move to technical design when
- [ ] PRD checklist all yes. [book ch6]
- [ ] Main user flows and first-version scope are clear. [book ch3]
- [ ] Every Must feature links to at least one REQ-###. [book ch6, addition on Must]
- [ ] Open questions blocking design are answered or owned. [book ch6]
- [ ] No technical decisions (persisted-data layout, frameworks, interface endpoints) appear in the PRD. [book ch6]

### Prompts (Part B)
**P2.4 Personas and use cases** [book ch6]
```
You are helping me prepare a product spec.
Project idea: [paste the project idea]
Known requirements: [paste the requirements]
Create [3] user personas. For each persona, include goal, frustration, main use cases, and success
condition. Keep the wording simple and practical. Do not invent technical implementation details.
```

**P2.5 PRD review** [book ch6]
```
Act as a strict product spec reviewer.
Review the PRD below for unclear goals, missing users, weak success metrics, bloated scope,
missing out-of-scope decisions, vague user stories, and missing user flows.
Return your response in four sections:
1. Problems found
2. Suggested improvements
3. Missing questions
4. Revised compact PRD
PRD:
[paste your PRD here]
```

Book inconsistencies resolved here are listed in docs/resolved/01-03.md.

## Role notes [addition]
- Requester/PM: owns problem, scope, priorities, metrics; answers open questions; approves the PRD.
- Builder: drafts requirements and PRD with the agent, challenges untestable wording, proposes AC.
- Reviewer/QA: runs P2.3 and P2.5, checks each requirement is testable and has failure and missing-state ACs.
- Approver: marks Approved at Gate A and Gate B. One person may hold all roles.
