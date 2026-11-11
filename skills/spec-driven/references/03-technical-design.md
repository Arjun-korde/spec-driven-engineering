# 03 - Technical Design

**Purpose:** Decide HOW the system is built before any code is generated: structure, data, interface contracts, validation, integrations, versioning. The technical spec turns product direction into build direction; the agent follows it instead of guessing. [book ch7]

**When to use:** After requirements (and product spec, if any) are approved; before writing tasks, tests, or code for a feature. Keep the first version simple: "A clear simple tech spec is better than a large document nobody can follow." [book ch7] Scale to the work: a small change needs a few lines in each section, not a document. [addition]

**Inputs:** Approved requirements (REQ-###, AC-###), product spec if any, constraints, existing code or systems, open questions from earlier stages.

**Output artifact:** `docs/technical-spec.md` (template: `templates/technical-spec.md`), plus as needed: `docs/decisions/ADR-###.md` (template: `templates/adr.md`), `docs/interface-contract.md` (template: `templates/interface-contract.md`), `docs/data-model.md` (template: `templates/data-model.md`).

**Gate (done-when):** [addition, derived from book ch7/ch8/ch9 checklists]
- [ ] Spec explains the difference between what (requirements) and how (this spec); no feature outside the approved requirements.
- [ ] System boundary states what is included and excluded; assumptions listed.
- [ ] Architecture style chosen from real project needs, with options compared BEFORE deciding.
- [ ] Each component states what it owns and what it must not do.
- [ ] An ADR exists for each decision with several realistic options (at least the main architecture decision), or the spec records "no ADR needed" with a reason; each ADR has Reason and Review trigger/date.
- [ ] Persisted data (if any): entities, fields, relationships, constraints, retention/deletion defined.
- [ ] Every interface (HTTP, CLI, library API, event, file) has a contract with success AND error shapes, written before implementation.
- [ ] Validation rules written before code; each maps to a requirement.
- [ ] Integrations: data sent/received, failure behavior, secret handling.
- [ ] Security, performance (project-set measurable targets), and error-handling sections filled or marked not applicable with a reason.
- [ ] Versioning/compatibility policy stated for every contract consumers depend on.
- [ ] Open decisions listed with owner and "block implementation? yes/no"; none that block remain unanswered. Open questions are not treated as assumptions. [book ch7, App C]
- [ ] Technical Spec Guardrail, verified before generating code [book App C]: requirements are mapped to modules; data models are named; interface contracts are defined; error states are documented; tests exist before implementation (planned in 06-testing.md); security rules are explicit (08-security-reliability.md); open questions are not treated as assumptions.
- [ ] Every important decision links to a REQ and will link to a task and a test (see 04-traceability.md).

---

## 1. Technical spec outline (neutral) [book ch7; generalized per [addition]]

Delete sections that do not apply. Rename to fit the stack. Give each interface-shaped section an "Applies when" note (e.g. "Interactive authentication: only if users sign in"; "Interface states: only if there is a screen") and use the non-interactive alternative otherwise (exit codes, stdout/stderr, flags, --help/--version, schedule, partial input, overlapping runs, idempotency, per-item results). [addition]

| # | Section | Define |
|---|---|---|
| 1 | System overview | Name, purpose (1-2 sentences), primary users/consumers, core capabilities, system boundary (includes / does not include), external dependencies, assumptions [book ch7] |
| 2 | Architecture overview | Style, main components, responsibility of each, data flow, state ownership, trade-offs [book ch7] |
| 3 | Interface surface (UI, CLI output, public API, report format) | Screens/commands/entry points, inputs, states (loading, empty, error, success, disabled, permission-denied), user actions, accessibility basics [book ch7, generalized] |
| 4 | Core logic / services | Business rules, authorization, validation, jobs, integrations; write rules in plain language first - each becomes a task, a test, then code [book ch7] |
| 5 | Data model | Entities, fields, relationships, constraints, indexes, retention/deletion [book ch7] |
| 6 | Interface contracts | Per interface: name, purpose, auth, input, success output, error outputs, validation [book ch7/ch9] |
| 7 | Security | Authentication, authorization, input validation, sensitive data, secrets, safe errors [book ch7] |
| 8 | Performance | Expected users/data size, response targets, known limits - measurable, sized to this version [book ch7] |
| 9 | Error handling | User messages, internal logging, retry rules, fallback [book ch7] |
| 10 | Open questions | Question, decision owner, must-answer-before-implementation yes/no [book ch7] |

Appendix C lists a different 10-field set: it adds Testing approach (unit, integration, end-to-end, security, performance) and Deployment approach (environment, configuration, migration, rollback, monitoring), names Architecture style options (monolithic, modular, service-based, event-driven, other), and has no separate Interface-surface section. In this skill, testing approach lives in 06-testing.md and deployment approach in 09-deploy-monitor-maintain.md; reference them from the spec or add the sections if the project wants one document. [book App C; placement addition]

Added by this skill: **Reliability** may be a separate section when failure recovery matters (the book names reliability but gives no section for it). [addition]

Questions the spec must answer: What is the system? How is it organized? What does the user/consumer interact with? What logic runs behind the scenes? How is data stored? How do parts communicate? How is quality protected? [book ch7]

### Rules
- Not a code listing; a design document that prepares tasks, tests, implementation. [book ch7]
- Set boundaries so the agent does not build more than asked. [book ch7]
- Core logic enforces its rules where the data lives and does not rely on the caller having validated; reject invalid input even if the client validated. [book ch7, generalized]
- Performance statements must be measurable (target + data size + measurement method). Weak: "loads fast". Strong: "[operation] completes within [N unit] for [data size]". The project sets N and records it; do not copy numbers from examples. [book ch7; addition on measurement method]
- Security is defined before implementation because generated code may choose weak defaults. [book ch7]
- Error handling: clear message to user/consumer, enough internal logging, no private details exposed. [book ch7]

### Per-stack illustrations (one line each) [addition]
- Web API: components = router, service, repository, queue worker.
- CLI tool: components = argument parser, command handlers, core library, output formatter.
- Library: components = public API, internal modules, test fixtures; "interface surface" = exported functions/types.
- Data pipeline: components = ingest, transform, validate, load, scheduler; state = checkpoints.
- LLM feature: components = request handler, context retriever, prompt builder, model client, output validator; add the grounding and review-before-save rules from your requirements.

---

## 2. Architecture options, trade-offs, ADRs [book ch8]

Architecture is the map of where responsibilities belong, how parts communicate, what stays separate, and what rules the agent must follow. It exists so the agent follows rules instead of guessing. [book ch8]

### Choosing a style
Choose by project size, team skill, expected growth, operational capacity - not by popularity. Ask: "What architecture gives this project enough structure without creating unnecessary complexity?" [book ch8]

| Question | Why it matters [book ch8] |
|---|---|
| How large is the first version? | Small product: simple structure first |
| How many people will work on it? | More people: clearer module boundaries |
| Will features change often? | Frequent change: separate areas |
| How much deployment complexity can you manage? | Distributed deployment creates operational duties |
| What must be protected? | Sensitive features need strong boundaries |

Deployment-unit options (server-style software; substitute equivalents for libraries, scripts, data work) [book ch8, generalized; the book's two tables are merged here]:

| Option | Best use | Strength | Cost / warning | Choose when |
|---|---|---|---|---|
| Single unit (monolith) | Small tools, MVPs, internal apps | Fast to build, easy to run | Messy without internal structure; do not let all logic mix in one file or layer | Very small applications |
| Single unit, internal modules (modular monolith) | Growing business systems | Balanced structure, simple deployment | Needs discipline: define modules and respect their boundaries | Most early business apps |
| Separate services (microservices) | Large systems with independent teams and scaling needs | Independent scaling and ownership | More infrastructure and coordination; do not choose it only because it sounds advanced | Only when scale or team structure demands it |

Default bias: for most early-stage projects start with internal modules. This is a default, not a rule; "very small" has no threshold in the book, so the project decides and records it in an ADR. [book ch8; addition on resolving "default vs. do not choose for me"]

### Components and boundaries
- Component = meaningful part with a clear responsibility. Boundary = what it owns and what it must not touch directly. Ask: What does it own? What does it need from others? What is it forbidden to do? [book ch8]
- A boundary is useful only when you can tell whether a piece of code belongs inside or outside it. [book ch8]
- Use an Owns / Must-not-do table. Book example layers (rename per stack): interface layer (owns display and user actions; no data queries or secret rules), entry/service layer (owns routing, input validation, response formatting; no hidden domain logic), domain module (owns business rules; no dependence on layout), data layer (owns persistence and queries; no user-facing behavior decisions). [book ch8, generalized]

| Component | Owns | Must not do |
|---|---|---|
| [name] | [responsibilities] | [forbidden] |

Diagrams: understandable, not beautiful: boxes = components/modules/services/stores/external systems; arrows = dependency or communication; boundary lines = where responsibility ends; short labels. A text/ASCII diagram is enough. [book ch8]
AI role: use the agent to compare options, expose missing concerns, and suggest risks. Do not let it decide silently; ask for trade-offs BEFORE a recommendation; the Approver makes the final decision. [book ch8]

### ADRs (Architecture Decision Records)
Short durable instructions: context, options, decision, consequences. Include the ADR in project context and tell the agent to follow it. [book ch8]
Write an ADR when: a choice has multiple realistic options, is costly to reverse, or constrains the agent's implementation. Do not write ADRs for trivial choices. [addition]

Fields (final set): ID `ADR-###`, Title, Status, Date, Context, Options considered, Decision, Reason, Consequences (positive, trade-off, rules the agent must follow), Security Impact, Reliability Impact, Performance Impact, Related Requirements, Related Technical Spec Sections, Review trigger/date, Superseded by. [book ch8, ch28, App K; merged]
Status values: Proposed | Accepted | Rejected | Superseded (ch8 says "Replaced"; App K says "Superseded"). When superseded, fill "Superseded by: ADR-###". [book ch8, App K; linkage addition]
Storage: `docs/decisions/ADR-###.md`, one file per decision; reference them from the agent's project context. The book ch4 workspace uses a single `decisions.md`; one file per ADR is this skill's choice. [addition]

---

## 3. Persisted data model [book ch9]

Purpose: define what the system must remember, protect, validate. Model meaning before storage syntax; "designing meaning before implementation." [book ch9]

Steps:
1. List entities (things the system must remember). Ask: what objects, what details describe them, how do they relate, what rule must always be true? [book ch9]
2. Define fields, types, required/optional, relationships, constraints (keys, uniqueness, allowed values), indexes for common lookups, deletion/retention behavior. [book ch9]
3. Keep it simple, explicit, easy to test; each entity one clear idea; each relationship supports a real requirement. [book ch9]
4. Make invalid data hard to store: do not rely only on application code to protect important rules. [book ch9]
5. Cross-check: every field used by a contract or validation rule exists in the model; every entity maps to a REQ. [addition; fixes book examples where a request field, a rule, and a membership concept had no matching model field]

Applies to any storage: relational, document, files, config, datasets, model artifacts. Skip if nothing is persisted. [addition]
Deletion behavior is required, not optional: choose per relationship (restrict, cascade, archive/soft-delete, retain-for-audit) and record why. The book names it but gives no options. [addition]
Isolation/ownership boundary (owner: user, project, or organization): if data belongs to an owner, require the owner key on every record and every query. [addition, from book ch27/ch28 pattern]

Per entity also record: purpose; default value and validation rule per field; relationship type (one-to-one, one-to-many, many-to-many); sensitive data (personal, confidential, security-sensitive fields); migration notes (how a change to the model is applied safely); retention rules. [book App E]

Template: `templates/data-model.md`.

---

## 4. Interface contracts [book ch9, generalized]

An interface contract is the exact shape of data entering and leaving a boundary, including invalid-input behavior. It lets every consumer, the tests, and the agent agree. Define it before implementation; keep example contracts inside the spec so the agent does not invent a different shape. [book ch9]

Per interface, specify: name/entry point, purpose, permission, input, success output, error outputs, validation rules, versioning notes. [book ch9] Appendix D adds: business rules enforced beyond basic validation, and tests required (unit, integration, edge case). [book App D]

Contract-first rule: define response shapes first, then implement producer and consumer against the same contract. [book ch27]

### Boundary kinds (use whichever apply) [addition]
| Kind | Name/entry | Input | Success output | Errors |
|---|---|---|---|---|
| HTTP/RPC | method + path or procedure | body, query, headers | status + body | status + error object |
| CLI | command + subcommand | args, flags, stdin, env | stdout format + exit code 0 | stderr message + non-zero exit code(s) |
| Library API | function/class signature | typed parameters | return type | exceptions/result type, documented |
| Event/message | topic/queue + event name | payload schema | emitted event schema | dead-letter/retry rule, error event |
| File/format | path pattern + format | schema, encoding, required fields | file written/parsed | rejection rules for malformed files |
| Model I/O (LLM/ML) | prompt/input schema | fields + limits | output schema | refusal/unsupported/invalid-output handling |

### Consistent error shape (one per project) [book ch9; resolved per CONVENTIONS]
Every error uses the same structure: `code` (stable machine-readable), `message` (safe, human-readable), optional `field` (when tied to one input). Stack traces and internal detail go to internal logs only. Apply the same shape to HTTP bodies, CLI stderr/JSON output, library error objects, and event error payloads. Do not mix bare-string errors with structured errors. [addition: unification]

Illustration (adapt per boundary):
```
{ "error": { "code": "VALIDATION_ERROR", "message": "The title is required.", "field": "title" } }
```
CLI: `error[VALIDATION_ERROR]: title is required (--title)` on stderr, with the exit code defined in the contract. Library: an exception or result type carrying code, message, field. [addition]

### Error response principles [book App D, written for HTTP statuses; apply the idea at any boundary]
- Success: return only the fields the caller is allowed to see.
- Invalid input (400): explain the invalid field without exposing internals.
- Not authenticated (401) / not allowed (403): ask to authenticate again / reveal no protected resource details. Applies only if callers authenticate; CLI, pipeline, and library contracts use exit codes or error types instead. [addition]
- Not found (404): do not confirm whether another owner's resource exists (a denied and a missing resource look the same to the caller). [security no-leak rule]
- Unexpected failure (500): safe generic message; log the internal reason.

### Consistency rules [book ch9]
- Every success returns a predictable shape.
- Every error uses the shared error shape.
- Every access to protected data checks permission before returning.
- Validation happens before data is saved.
- Important create and status-change events are recorded. [book ch9] Whether an audit trail is required is a per-project choice. [addition]

Template: `templates/interface-contract.md`.

---

## 5. Validation [book ch9]

Validation runs before data is saved. Write rules before code; each rule maps to a REQ/AC. Rule types: required field; length; allowed values; relationship (e.g. referenced item belongs to the owner); permission; date/range. [book ch9]
Use one vocabulary of allowed values across schema, validation, and examples (the book lists `todo, doing, done` in one place and `todo, doing, blocked, done` in another; define the set once in the data model and reference it). [addition]

Group rules per interface: required fields, allowed values, relationship rules, permission rules, error responses. Each rule yields at least one failure check later. [book ch9; addition on test link]

---

## 6. Integrations [book ch9]

Specify before implementation: external services can fail, change, rate-limit, or return unexpected responses. For each integration record: provider, purpose, data sent (exact fields), data received (exact fields), failure behavior (retry, show message, log, queue for later), security rule. Examples: payment, email, calendar, identity, storage, analytics, AI model APIs. [book ch9]
Security reminder: never design an integration that exposes secrets to untrusted parts of the system or stores tokens in plain text; keep secrets out of source and examples. [book ch9, ch7]
Add per integration: timeout, retry limit, idempotency expectation, how to run tests without the real service. [addition]

---

## 7. Versioning and compatibility [book ch9]

Decide the versioning mechanism per contract (version in path, header, schema version field, package semver, CLI major version). Introduce a new version for breaking changes. [book ch9, generalized]

Classification [book ch9]: usually safe = add an optional field/output, add a new operation/endpoint/command. Breaking = rename a field, remove a field, change a data type.

Before changing a contract, list all consumers (clients, tests, docs, integrations) that need updating. [book ch9 prompt 9.3, restated as a rule]

---

## 8. Open decisions: do not guess [book ch7, App C]

- Record every undecided technical point in section 10 of the spec (question, decision owner, answer needed before implementation yes/no). Open decisions must not be guessed by the agent. [book ch7, App C] Assumption vs open decision is defined in `01-intent.md`; record assumptions in the spec's Assumptions table. [addition]
- The agent must stop and ask rather than choose silently on: architecture style, data fields not in a requirement, new interfaces, permission rules, retention/deletion, integration failure behavior, numeric targets. [addition]
- A decision resolved in chat is not resolved until it is written in the spec or an ADR. [addition]
- Items marked "blocks implementation: yes" must be closed before implementation (book) and, in this skill, before tasks are written. [book ch7, App C; "before tasks" addition]

---

## Prompts

Prompts are addressed to the agent. Replace [placeholders]. Parameterize audience (e.g. "explain for [audience level]").
### P3.1 Explore architecture options [book ch7]
```
You are helping me write a Technical Specification.
Requirements / product spec:
[paste]
Suggest 2 simple architecture options for this project. For each option, explain:
1. Main components
2. Data flow
3. Advantages
4. Risks
5. Which option is better for [first version / my team and constraints]
Do not write code yet. Do not add features outside the requirements.
```

### P3.2 Compare deployment-unit options [book ch8]
```
Given this project summary, compare three architecture options: [single unit], [single unit with internal modules],
and [separate services]. For each option, explain the benefits, risks, complexity level, and when I
should avoid it. Do not choose for me until you have compared the trade-offs.
[project summary]
```

### P3.3 Draft the technical spec [book ch7, adapted: sections renamed neutrally; "omit sections", audience, and "list under Open Questions with a decision owner" rules added]
```
You are helping me create a Technical Specification.
Use these requirements: [paste]
Create a concise Technical Specification with these sections: System Overview, Architecture Overview,
Interface Surface, Core Logic, Data Model, Interface Contracts, Security, Performance, Error Handling,
Open Questions. Omit sections that do not apply and say why.
Rules:
- Do not write implementation code yet.
- Do not add features outside the requirements.
- Use language suited to [audience level].
- Make each decision clear enough to become tasks and tests later.
- Do not guess missing decisions; list them under Open Questions with a decision owner.
```

### P3.4 Review architecture [book ch8]
```
Review the architecture below. Identify missing components, unclear boundaries, security risks,
performance risks, and places where business logic may become scattered. Explain your
reasoning in language suited to [audience level]. Do not rewrite the architecture until you have listed
the issues.
[architecture]
```

### P3.5 Draft an ADR [book ch8, adapted: the book prompt names "the architecture style"; review trigger or date added from ch28]
```
Using the project requirements and technical specification below, draft an Architecture
Decision Record for [decision]. Include context, options considered, decision, reason,
consequences, implementation rules for the AI assistant, and a review trigger or date.
[requirements and spec]
```

### P3.6 Identify entities [book ch9]
```
Using the requirements below, list the main entities this system must store. For each entity,
identify important fields, relationships, and business rules. Do not create storage definitions yet.
Focus only on meaning and relationships.
```

### P3.7 Review validation coverage [book ch9]
```
Review the interface below. List all validation rules that should exist before data is saved.
Group them into required fields, allowed values, relationship rules, permission rules, and error
responses. Do not write code yet.
```

### P3.8 Check compatibility risk [book ch9]
```
Review this interface change and tell me whether it is backward compatible. Identify any consumer,
test, documentation, or integration updates that may be needed. If it is a breaking change,
suggest a safer versioning strategy.
```

### P3.9 Tech-spec reviewer [addition]
```
Review the technical specification below against the approved requirements. Do not rewrite it yet.
Report findings in these groups, citing section and REQ-### for each:
1. Scope: anything not traceable to a requirement; requirements with no design coverage.
2. Boundaries: components without owns/must-not-do; logic that could scatter across components.
3. Data: fields used in contracts or rules but missing from the data model (and the reverse); undefined
   deletion/retention; inconsistent allowed-value sets.
4. Contracts: interfaces missing a success shape, error shape, permission, or validation; error shapes
   that differ between interfaces; examples that contradict the schema.
5. Security and secrets: missing authentication/authorization, secrets exposure, unsafe errors.
6. Performance and reliability: targets that are not measurable or lack data size and measurement method.
7. Integrations and versioning: missing failure behavior, timeouts, breaking-change policy.
8. Decisions: ADRs missing Reason or Review trigger/date; open questions that block implementation; places
   where you would have to guess.
For each finding give severity (P0 blocks release or risks data loss/security, P1 major, P2 minor, P3 cosmetic) and the smallest fix. End with a verdict:
Approved | Needs update.
```

### P3.10 Generate frontend components from the spec (UI projects only) [book ch27, neutralized]
```
Use the dashboard (or screen) requirements, the interface contracts, and the permission rules below.
Create a component plan before writing code.
For each component, include props, state, loading behavior, empty state, error state, and
accessibility notes.
Do not invent new metrics, roles, or interfaces.
[requirements, contracts, permission rules]
```
Book component spec table columns: component, purpose, data needed, states, rules (e.g. "do not show content until access is confirmed"; "empty charts must not appear as zero performance"). [book ch27]

---

## Common mistakes
- Letting the agent pick the architecture while writing code. [book ch8]
- Choosing separate services because it sounds advanced. [book ch8]
- Fields appearing in examples but not in the data model; rules referring to concepts with no model (e.g. membership). [addition]
- Different error shapes per interface. [addition]
- Validation added after bugs rather than before implementation. [book ch9]
- Vague performance words with no target. [book ch7]
- ADRs with no consequences for the agent, no reason, or no review trigger. [addition]
- Guessing an open decision to keep moving. [addition]

Book inconsistencies resolved here are listed in docs/resolved/01-03.md.

Role notes: [addition] Requester/PM confirms boundaries, priorities, and answers open questions; Builder/Agent drafts options, spec, ADRs, contracts, and stops on open decisions; Reviewer/QA applies P3.9 and checks contracts are testable; Approver makes the final architecture decision and marks the spec Approved.
