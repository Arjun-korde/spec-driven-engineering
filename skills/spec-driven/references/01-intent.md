# 01 - Intent: from raw idea to Engineering Intent

**Purpose:** Turn a raw idea into a one-page Engineering Intent (problem, users, goals, scope, constraints, success criteria) before any requirement, task, or code exists. [book ch2]
**When to use:** At the start of a project or a large feature; whenever a request is vague ("build a school app", "make it better"). Skip or shrink for a tiny, fully understood change. [addition]
**Inputs:** A raw idea, request, or business need in plain language; any known users, constraints, deadlines.
**Output artifact:** `docs/intent.md` (template: `templates/intent.md`). The book also calls this stage output a "project brief"; use the name "Intent". [book ch1, ch2, ch4]
**Gate (done-when):** see "Gate" below.

## Why spec-driven (short)
- Core chain: idea -> requirement -> specification -> task -> test -> reviewed code. Done means the implementation traces back to a requirement and is verified by tests. [book ch1]
- A vague prompt hides decisions; the agent fills the gaps with invisible assumptions. You cannot review what you never defined. [book ch1]
- Exploration is not implementation: brainstorm and compare freely, but once a decision is made, write it as a requirement before asking for implementation. First explore and clarify, then specify and build. [book ch1]
- A good prompt is not a replacement for a good specification: a prompt says what to do next; the spec says what the software is to become. [book ch1]
- The human decides the stage and supplies the artifact; the agent helps only with the current step and does not choose the lifecycle. [book ch3]
- Never ask the agent to guess the whole project at once. [book ch1]

## Procedure
1. Capture the raw idea in plain language. Write vision (what outcome) separately from implementation ideas (how); do not mix them in one thinking stage. [book ch2]
2. Run the ambiguity test on every sentence. Ask clarifying questions (below). Do not design the solution yet. [book ch2]
3. Write a problem statement: who is affected, what difficulty they face, what happens because of it, what improvement the software should create. [book ch2]
4. Name primary users (never "everyone"); add secondary users only if their needs differ enough. [book ch2]
5. Write goals as outcomes, not features. Feature: "create task comments". Goal: "make task discussions easier to follow". [book ch2]
6. Translate the business need into software direction: main capabilities, key data, quality expectations, exclusions for the first version. [book ch2]
7. List first-version capabilities, out-of-scope items, constraints, risks, success criteria. Remove dangerous ambiguity (wrong feature, workflow, data model, security behavior), not every unknown. [book ch2]
8. Run the gate. No code is written at this stage; that is intentional. [book ch2]

## Ambiguity test
"Could two competent developers build two different things from this instruction? If yes, the instruction needs more detail." [book ch2]

| Ambiguous request | Why dangerous | Clearer version |
|---|---|---|
| Users can create tasks. | No fields, ownership, deadlines, permissions. | A signed-in user can create a task with title, description, due date, priority, and assignee. |
| Admins can manage users. | "Manage" undefined. | An admin can view users, deactivate accounts, reset roles, and see account status. |
| The dashboard should show reports. | Reports and period undefined. | The dashboard shows daily totals for active users, completed tasks, overdue tasks, and new comments. |
| The app should be secure. | Too broad to test. | The app requires login, role-based access, input validation, and safe error messages. |

[book ch2] (Domain examples are illustrative; substitute your own, e.g. a CLI flag set, a library function, a pipeline stage.) [addition]

## Clarifying questions

| Question | What it uncovers |
|---|---|
| Who is this for? | The actual user, not just the person requesting the system. |
| What problem hurts enough to solve? | The business or user pain behind the project. |
| What outcome should improve? | The measurable result the software should produce. |
| What must the system not do? | Boundaries that prevent scope creep. |
| What constraints already exist? | Budget, time, device, data, security, process, or team limits. |

[book ch2] Ask the smallest set of questions needed; batch them in one message. [addition]

After the generic questions, pick the project profile and also ask its Intent-time questions (see `12-project-types.md` section 2, "Ask at Intent time" under your profile). [addition]

### Assumption vs open decision [addition]
- Assumption: a provisional choice that is recorded with a reason and a confirm-by owner/date. It does not block work, unless it affects scope, security, data, contracts, or cost; then it is an open decision.
- Open decision: blocks progress and must not be guessed. Ask, or record it in Open Questions with an owner.
- Record assumptions in the Assumptions table (Assumption | Why | Confirm by | Status open/confirmed/rejected) of the requirements and technical-spec templates; intent keeps Open Questions. Other references link here.

### Vision vs implementation
| Vision statement | Implementation statement |
|---|---|
| Customers place orders without calling the office. | Needs an order form, persisted data, notifications, an admin view. |
| Students find class materials faster. | Needs search, categories, upload, access control. |
| Support staff answer repeated questions faster. | Needs a knowledge base, retrieval workflow, conversation logs. |

[book ch2]

### Problem statement: weak vs stronger
| Weak | Stronger |
|---|---|
| We need a task app. | Small project teams lose track of task ownership and deadlines, causing missed work and repeated status meetings. The system should help each person see assigned work, due dates, and progress clearly. |
| We need a chatbot. | Support staff answer the same basic questions daily, slowing response time. The system should help customers get accurate answers from approved knowledge before escalating to a person. |

[book ch2]

### Users, goals, constraints
| Element | Question | 
|---|---|
| Primary user | Who uses the system most often? |
| Secondary user | Who reviews, manages, or supports it? |
| Goal | What should improve after it exists? |
| Constraint | What must limit the design? (time, platform/device, data, security, budget, process) |
| Risk | What could make the project fail? |

[book ch2] Platform (web, mobile, CLI, batch, embedded) is a constraint to fill in, not an assumption. [addition]

## Intent fields
Project name; problem statement; primary users; secondary users; business or user goals; core capabilities; out of scope; constraints; risks; success criteria. [book ch2]

Appendix A adds: business goal (what success means for the owner); user goal; current pain points; desired outcome (the state that is true once the solution works); success measures ("3-5 measurable signs"); open questions. Use these where the chapter list is too thin; pain points and desired outcome sharpen the problem statement, and open questions are a field of the template. [book App A]

Project type / profile (web app, CLI, library, data pipeline, LLM feature, other; see 12-project-types.md) is recorded as a constraint line. [addition]

Success criteria are product-level ("a lead can see overdue tasks from one view"); acceptance criteria (file 02) are requirement-level. Keep them measurable but do not copy numeric values from examples; record your own. [addition]

## Gate (done-when)
Confirm before moving to requirements: [book ch2]
- [ ] A clear problem statement.
- [ ] Primary and secondary users defined.
- [ ] Vision separated from implementation details.
- [ ] First-version capabilities listed.
- [ ] Out-of-scope listed.
- [ ] Constraints and risks identified.
- [ ] Simple success criteria defined.
- [ ] Self-check: the document makes the next step (requirements) easier; if not, it is too vague. [book ch2]
- [ ] Open questions are captured instead of hidden. [book App A]
- [ ] Each open question has an owner; none that change scope, data, or security remain unasked. [addition]

Intent Quality Checklist [book App A]:
- [ ] The problem is stated without assuming a specific technical solution.
- [ ] The intended users are named clearly.
- [ ] The desired outcome can be measured or observed.
- [ ] Out-of-scope items are written before implementation begins.
- [ ] Open questions are captured instead of being hidden.

## Prompts
Placeholders in [square brackets]. Wording is neutralized from the book (e.g. "software" dropped). Common pattern: role/context, pasted input, explicit "Return:", explicit "Do not". [addition, synthesized from the prompt boxes in ch1-3]

**P1.1 Clarify a raw idea** [book ch2]
```
You are helping me clarify a raw idea before writing requirements.
Idea: [paste the idea here]
Ask me the smallest set of questions needed to identify the problem, users, goals, constraints,
and scope boundaries. Do not design the solution yet.
```

**P1.2 Improve a problem statement** [book ch2]
```
Rewrite this vague request as a clear problem statement.
Request: [paste request]
Use this structure: affected user, current difficulty, consequence, desired improvement. Keep it
specific and do not invent features.
```

**P1.3 Translate business need into direction** [book ch2]
```
Convert this business need into direction for what to build.
Business need: [paste need]
Return: primary users, main capabilities, key data, quality expectations, and what should be
excluded from the first version.
```

**P1.4 Create the Engineering Intent** [book ch2]
```
Use the information below to draft an Engineering Intent document.
Project idea: [paste idea]
Known users: [paste users]
Known business need: [paste business need]
Known constraints: [paste constraints]
Return a concise document with: problem statement, users, goals, core capabilities, out of scope,
constraints, risks, and success criteria.
```

**P1.5 Gate review (use at every stage)** [book ch3, adapted]
Reworded: the book ends this prompt with "Do not move to implementation", which does not work for late stages.
```
Act as a spec-driven reviewer.
Current stage: [stage name]
Artifact: [paste artifact]
Check whether this artifact is ready for the next stage. Identify missing information, vague
statements, risky assumptions, and the exact corrections needed. Do not start work belonging to
a later stage.
```

## Common mistakes
- Starting from a feature list instead of a problem. [book ch2]
- "Everyone" as the user; goals written as features. [book ch2]
- Mixing named technology choices into the vision. [book ch2]
- Treating exploration output as a decision without writing it down. [book ch1]
- Asking for the whole product in one prompt. [book ch1]

Book inconsistencies resolved here are listed in docs/resolved/01-03.md.

## Role notes [addition]
- Requester/PM: supplies the idea, answers clarifying questions, owns the problem statement and out-of-scope list.
- Builder: asks the questions above, drafts the intent, flags risks and unknowns.
- Reviewer/QA: runs the ambiguity test and gate; confirms success criteria are checkable.
- Approver: signs off on the intent before requirements begin. One person may hold all roles.
