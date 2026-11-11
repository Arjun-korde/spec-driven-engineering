# 14 - Glossary and Tool Categories

## Purpose
Quick reference for key terms and for choosing tools by category, not by brand. [book App R, App S]

## When to use
When a term is unclear, or when picking or reviewing the tools that support the workflow.

## Inputs
A term, or the team's current tool set and constraints (budget, privacy, environment).

## Output artifact
A shared understanding of terms; a recorded tool choice per category.

## Gate (done-when) [addition]
- [ ] Each tool category the project needs has a chosen tool, or a stated "none".
- [ ] The Tool Selection Checklist was answered for each new tool.

---

## 1. Glossary [book App R]
Definitions follow the book, shortened. Project IDs (REQ-###, AC-###, TASK-###, TEST-###, ADR-###, SEC-###, BUG-###, CHG-###, US-###) are defined in SKILL.md. [addition]

| Term | Definition |
|---|---|
| Acceptance criteria | Clear conditions that prove a requirement has been met |
| Agent | An AI system or assistant that performs a defined engineering task using context and instructions |
| Architecture decision record (ADR) | A short document explaining an important technical decision and its trade-offs |
| Context pack | A focused package of project information given to an agent for a specific task |
| Continuous integration | A workflow that checks code changes with automated tests and validation |
| Database migration | A controlled change to the structure or content of persisted data |
| Deployment | Releasing software into an environment where users or systems can use it |
| Engineering intent | The early statement of why a project exists and what outcome it should create |
| Integration test | A test that checks whether connected components work together |
| Non-functional requirement | A requirement about quality attributes such as security, reliability, performance, or usability |
| Product requirements document (PRD) | Describes what the product or feature must do and why it matters |
| Refactoring | Improving code structure without changing intended behavior |
| Requirements traceability matrix | A table connecting requirements to specs, tasks, tests, code, and review evidence |
| Rollback | A planned way to return to a safer previous state after a failed release |
| Spec drift | The gap that appears when actual behavior no longer matches the written specification |
| Technical specification | Explains how approved product requirements will be designed and implemented |
| Unit test | A test for one small piece of behavior, usually a function or class |
| Version control | A disciplined way to track, review, and recover changes to files over time |

## 2. Tool categories [book App S]
The workflow does not depend on a specific platform. Choose tools that fit team, budget, privacy needs, and environment; the categories matter more than any brand. [book App S]

| Category | What you need from it | Examples or options |
|---|---|---|
| Writing and specs | A place for PRDs, technical specs, templates, decision records | Word processor, Markdown editor, internal wiki, shared folder |
| Version control | Track changes, review work, recover older versions | Local Git, hosted platforms, internal systems |
| AI coding assistant | Works from project context, generates code, explains changes, helps review tests | Chat assistant, IDE assistant, agentic coding tool |
| Code editor | Search, formatting, terminal access, project navigation | Any modern IDE or editor |
| Test runner | Repeatable unit, integration, end-to-end, security, regression runs | Language-specific frameworks and command-line tools |
| Container runtime | Package and run applications consistently across environments | Container tools or platform-native containers |
| Database tooling | Local development, migrations, backup, restore, inspection | Database clients, migration scripts, admin consoles |
| CI/CD | Repeatable checks, builds, tests, deployment, rollback | Platform pipeline, local scripts, internal build system |
| Monitoring and logs | Visibility into errors, performance, usage, production health | Logging system, metrics dashboard, error tracker, uptime checks |
| Security review | Dependency checks, secret detection, access review, validation testing | Static analysis, dependency scanner, secrets scanner, manual checklist |

Skip categories the project does not need (a CLI tool may need no container runtime or database tooling). [addition]

### Tool Selection Checklist [book App S]
- [ ] The tool supports your workflow without forcing unnecessary complexity.
- [ ] The team can use it consistently.
- [ ] It supports review, traceability, and rollback where needed.
- [ ] It does not require exposing private data unnecessarily.
- [ ] It fits the project's budget, skill level, and deployment environment.

Tools change; the discipline stays: clear intent, testable specs, specs connected to tasks and tests, careful review of AI output, deployment with rollback plans, and specs kept alive after release. [book App S]

Role notes: Requester/PM keeps terms consistent in requirements; Builder picks and records tools per category; Reviewer/QA applies the Tool Selection Checklist, especially the private-data and traceability items. [addition]
