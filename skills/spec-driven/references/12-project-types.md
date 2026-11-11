# 12 - Adapting the Stages to Project Types

**Purpose:** Show how to apply the stage pipeline to different project types, extract reusable patterns from the book's four worked projects, and give a retrofit path for existing codebases.

**When to use:** At project start (choose a profile), or when a stage template assumes something your project does not have (UI, persisted data, network service).

**Inputs:** Project type; the base stage artifacts (intent, requirements, design, tasks, tests, review, release).

**Output artifact:** A project profile note: which stage artifacts apply, which are added, which are skipped (and why).

**Gate (done-when):** [addition]
- [ ] Profile chosen and recorded in the intent doc.
- [ ] Each skipped artifact has a one-line reason.
- [ ] Each profile-specific check below appears in requirements or tests.
- [ ] Every requirement states its failure/empty/blocked behavior where applicable.

Core idea: the book builds a "spec pack before code"; the agent never gets "build X", it gets connected artifacts that each trace to requirements. [book ch 25-28] Scale depth to project size. [book ch 25, 30]

---

## 1. Patterns from the worked projects
Domain details (task app, support bot, dashboard, workspace app) are illustrative; the patterns are the reusable part.

| Pattern | Rule | Source |
|---|---|---|
| Boundary table first | Write a short table: type, users, core entities/capability, out-of-scope, quality priority | [book ch 25, 26, 27] |
| Stable IDs | REQ IDs appear again in design, contracts, tests, tasks | [book ch 25] |
| One artifact, one table, one prompt | Each artifact is a compact table plus a ready prompt (observation from the worked projects) | [addition] |
| Define data meaning before contract before UI/consumer | Dashboards fail when data meaning is unclear; contracts come from requirements and data needs, not guessed after the screen | [book ch 27] |
| Contract-first across layers | Define response shapes first, then implement all sides against the same contract | [book ch 27] |
| Four state requirement | Every requirement says what happens when data is missing, delayed, empty, or blocked by permission; empty is not zero | [book ch 27] |
| Server-side enforcement | Hiding a control is not security; enforce authorization where the data lives | [book ch 27] |
| Isolation boundary tested explicitly | Choose the owner/boundary (user, project, org); require it on every record and query; test cross-boundary access (can A reach B by guessing an ID?) | [book ch 25, 27] |
| Small agent tasks with stop conditions | Each task: input artifacts, acceptance check, tests to run, stop condition. Split if it touches several concerns at once | [book ch 25, 26] |
| Context pack per task | Task ID, goal, requirements, data rules, interfaces, tests to satisfy, stop condition | [book ch 25] |
| Thin vertical slices with exit rules | Build smallest useful slice, test and review it; each phase has a verification and exit rule | [book ch 28] |
| Controlled change | One task per branch/change set; small commits naming requirement and task; failing checks do not merge. Works with any version control or a review folder | [book ch 28] |
| Review against artifacts | Compare code to requirements, not "does the demo work" | [book ch 25] |
| Refactoring rule | Refactoring keeps behavior; if behavior changes it is a requirement/design change and must be documented | [book ch 25] |
| Release by evidence | Deploy when accuracy, permissions, performance, error behavior, rollback are ready, not because the page loads | [book ch 27] |
| Production review feeds the spec | Real usage reveals missing cases; improve the spec, do not blame the implementation | [book ch 26] |
| ADRs for decisions | Decisions recorded with context, options, consequences, review trigger so they do not vanish into chat | [book ch 28] |
| Traceability gap rule | A requirement with no test, no task, or no review evidence is not ready for production | [book ch 28] |
| Safe sample data | Use sample data only; no live customer records, payment systems, or private production data in dev | [book ch 26] |

## 2. Profiles
Each profile lists what to add or adapt per stage. Where the book covers it, cited; otherwise [addition]. Each profile also has an "Ask at Intent time" list [addition]: ask these during stage 1 (see 01-intent.md), alongside the generic questions, and record the answers as constraints, requirements, or Open Questions. Unanswered ones that affect scope, data, or security are open decisions, not assumptions.

### 2.1 Web or mobile app
- Intent/requirements: roles, journeys, screens; each requirement with loading, empty, error, permission-blocked states. [book ch 27]
- Design: component spec (purpose, data needed, states, rules); role x capability matrix; accessibility notes. [book ch 27]
- Tests: unit, interface contract, integration, end-to-end, security (cross-boundary), performance. [book ch 27]
- Release: config check, data migration tested on staging data, test gate, smoke test, rollback trigger. [book ch 27]
- Mobile: add store-release and offline/connectivity states. [addition]
- Ask at Intent time [addition]: Which roles and which journeys matter most? Which browsers, devices, or OS versions? What must work offline or on a weak connection? Accessibility and language needs? Who owns content and data, and what personal data is collected? How do users sign in or recover access? How is it released (store review, staged rollout)?

### 2.2 API or backend service
- Interface contract per operation tied to a REQ: authentication, inputs, expected behaviors, error cases. [book ch 25]
- Persisted data spec: entities, fields, rules, ownership. [book ch 25]
- Tests mapped to REQ IDs: normal, validation, authorization, error, edge. [book ch 25]
- Release: migration, health checks, monitoring, rollback. [book ch 27]
- Add versioning and compatibility rules for consumers. [addition]
- Ask at Intent time [addition]: Who are the consumers (internal, partners, public) and how do they authenticate? What data does it own and what is the isolation boundary? Expected volume, latency, and availability? Error shape and versioning promises to existing consumers? Rate or abuse limits? Which systems does it depend on, and what happens when they are down?

### 2.3 CLI, library, or SDK [addition]
- "Interface" = commands/flags, exit codes, public functions, file formats; contract lists inputs, outputs, errors.
- Requirements: behavior per command or function, including invalid input and missing-environment cases.
- Tests: automated checks on the public interface plus compatibility checks for previously documented behavior.
- Release: version label, change notes, package publish step, rollback = withdraw/yank and republish.
- Skip: screens, roles matrix (unless the tool has permission levels).
- Ask at Intent time [addition]: Which platforms and how is it distributed and versioned? Which flags and commands, including --help and --version, and are flags allowed after positional arguments? What are the exit codes? Is the output format a stable contract for scripts or CI (ordering, color, TTY detection)? What goes to stdout vs stderr? Which untrusted input does it read (size limits, loops)? Is network access allowed by default? What if it is interrupted or output is partial? Which standard or spec defines ambiguous rules? Which operating-system differences affect tests?

### 2.4 Data or ML pipeline [addition, patterns from book ch 27 data-meaning rule]
- Define data meaning first: each field/metric definition, source, freshness, owner.
- Requirements: correctness checks, missing/late/duplicate data behavior, reruns safe (idempotent).
- Tests: sample-data checks on transforms; reconciliation to source; failure and partial-run cases.
- Release: backfill plan, monitoring for sudden drops or impossible values, rollback = restore prior output. Maintenance watch items (listed in this order): metric accuracy, performance, error tracking, user feedback, spec drift. [book ch 27]
- Ask at Intent time [addition]: What schedule, and which timezone defines "a day" (cut-off, DST, holidays, send when empty)? What is late, partial, empty, or duplicate input, and is the source file complete when the job starts? What key identifies a record, and which source wins on conflict? What happens on a rerun, a skipped run, or two overlapping runs (idempotency)? Who is notified of failure, how, and what counts as visible failure? Who exactly receives output, and are results tracked per recipient or per item? Who owns credentials and the host, and what is the deadline (SLA)? Retention and confidentiality of outputs?
- For models: record training data version, evaluation measure, and the acceptance level the project chooses; do not copy numbers from other projects.

### 2.5 LLM or AI feature [book ch 26, 28]
Apply only when the project includes an LLM feature.
- Source-of-truth rule: answer only from approved knowledge; if not found, ask a clarifying question, state the limitation, or escalate. [book ch 26]
- Knowledge items carry metadata (id, title, category, last updated, owner, confidence notes) and an update/expiry rule. [book ch 26]
- Define the workflow as states with exit conditions, not "a chatbot". [book ch 26]
- Ask at Intent time [addition]: What is the approved source of truth, and who owns and updates it? What must the feature refuse or escalate, and to whom? What personal or sensitive data may enter prompts or logs? How will quality be judged (grounding checks, review samples), and what acceptance level will the project set? What happens when the model or provider is unavailable? Who reviews output before it is saved or sent? What cost or latency limits apply?

Conversation-state table (example for a support assistant; adapt states to your feature) [book ch 26, adapted]:

| Conversation state | Assistant behavior | Exit condition |
|---|---|---|
| Greeting | Welcome the user and ask how it can help in a simple, professional tone | The user submits a question |
| Intent detection | Identify the likely topic (e.g. account, billing, feature use, troubleshooting, policy, other) | A topic is selected or clarification is needed |
| Clarification | Ask one focused question if the request is incomplete or ambiguous | The user gives enough detail or the issue is escalated |
| Retrieval | Search approved knowledge for relevant policy, FAQ, or troubleshooting content | Relevant sources are found or confidence is low |
| Answer drafting | Explain the answer in plain language, with steps where helpful | Answer is grounded and safe |
| Escalation | Summarize the issue and route it to a human when required | Case is marked for review |

Vision-refinement prompt for an AI-feature project (to the agent) [book ch 28, adapted]:
```
You are helping me refine an AI-powered [project type]. Do not write code. Convert this vision into scope, non-goals, user groups, risks, assumptions, and measurable release success criteria.
[vision]
```
- Retrieval design: normalize, classify, retrieve, rank (recent, official, topic-matched), confidence check against a defined rule, draft from selected sources only, record trace (source IDs, confidence reason, escalation). Choose your own threshold and record it. [book ch 26]
- Security/privacy rules (SEC-###) written before implementation: never ask for secrets, minimize personal data, escalate sensitive cases, log carefully. [book ch 26]
- Review-before-save: agent output is a draft until a human approves. Cite approved sources; say "not supported" when context is missing; log unsupported cases. [book ch 28]
- Grounding test (Given no approved knowledge / When asked / Then say unsupported, no invented value, log the case). [book ch 28]
- Production review: accuracy, escalation quality, knowledge gaps, privacy, performance, spec drift. [book ch 26]
- Feedback loop updates prompts, specs, and tests. [book ch 28]

### 2.6 Embedded or other [addition]
- Interface = hardware/protocol/timing contract; requirements include resource limits and failure/safe states.
- Tests: simulated and on-device checks; define which cannot be automated and how they are evidenced.
- Release: flashing/rollout and recovery path replace deploy/rollback; keep version labels on firmware and spec.
- Other (docs, infrastructure, research): keep intent, testable acceptance criteria, review evidence, and update-on-change; drop the rest.

### 2.7 Existing or legacy codebase (retrofit path) [addition]
The book assumes new projects. Retrofit incrementally; do not document everything first.
1. Pick one area you are about to change (a feature or bug). Do not retrofit the whole system.
2. Write intent for that change only: problem, users, constraints, non-goals.
3. Reverse-document current behavior for that area as requirements, marking each "observed" vs "intended". Ask the agent to draft from code, then verify by hand; unverified items stay open questions.
4. Write characterization checks: automated checks that pin current behavior (normal, edge, failure) before changing anything.
5. Record constraints from existing structure (module boundaries, naming, data shapes) in the technical spec; forbid unrelated files in every agent task.
6. Make the change through the normal stage pipeline: tasks, tests first, review, update spec and change log.
7. Update traceability only for requirements you touched; coverage grows with each change.
8. Reuse the maintenance rule: when behavior changes, the spec changes (see `13-governance-and-metrics.md` section 2; record changes with `templates/spec-change-record.md`).
Gate: [ ] area bounded [ ] current behavior pinned by checks [ ] change traced REQ -> TASK -> TEST [ ] spec reflects result.

## 3. Choosing depth
Small feature: intent in a few lines, requirements, one task, normal/edge/failure checks. Full application: the whole artifact set. [addition] If the process is too heavy people avoid it; if too weak the agent fills gaps with assumptions; be strict where failure is expensive. [book ch 30]

## Common mistakes
- Applying web-app artifacts to a project with no UI or persisted data instead of dropping them.
- Treating empty/error/blocked behavior as an implementation detail.
- Retrofitting by documenting the entire legacy system before changing anything.
- Copying thresholds (confidence, performance) from examples instead of setting your own.

Book inconsistencies resolved here are listed in docs/resolved/10-14.md.

## Role notes [addition]
- Requester: picks the profile, supplies domain rules and the empty/error/blocked expectations.
- Builder: adapts design artifacts to the profile; owns the retrofit characterization checks.
- Reviewer/QA: checks the profile-specific items above in review.
