# 09 - Deploy, Monitor, Maintain

## Purpose
Plan release, observation, and spec upkeep before code is written, so the work has a safe path to release, can be monitored, and can be rolled back; then keep the spec true after release. "Finished" means: can be released safely, configured correctly, monitored, and rolled back. [book ch 23]

## When to use
- Deployment plan: draft it early, before implementation, then finalize it at release (stage 9). It shapes config values, health checks, startup, migrations, workers, logging. Do not ask the agent to "make this production ready" after the code is messy. [book ch 23]
- Monitoring/maintenance: define before deployment; run after every release, after each serious production issue, and before asking the agent for a major change. [book ch 24]

## Inputs
Approved requirements, technical spec, test plan, security rules (08), reliability spec (08).

## Output artifacts
`templates/deployment-plan.md` (draft before building, finalize at release), `templates/monitoring-maintenance.md`, `templates/spec-change-record.md` (the single change-record format).

## Gate (done-when)
Status values: Not started | Ready | Blocked | Done. [addition]

Before building (draft the deployment plan now; finalize it at release):
- [ ] Deployment plan answers: where it runs, what configuration it needs, how it is built and tested, how success is verified, how failure is recovered. [book ch 23]
- [ ] Config keys are defined, with secret/not-secret noted. [book ch 23]
- [ ] Rollback path is defined before deployment begins. [book ch 23]
- [ ] Monitoring signals and owners are defined in the spec. [book ch 24]

Before release (production readiness, from book App N):
- [ ] Released behavior matches approved requirements and acceptance criteria.
- [ ] Unit, integration, end-to-end, security, and regression checks passed.
- [ ] Configuration documented and separated by environment; no secrets in source.
- [ ] Deployment plan finalized: placeholders replaced with the real commands, config keys, and owners.
- [ ] Persisted-data changes reversible or safely recoverable (if applicable).
- [ ] Authentication, authorization, validation, secrets reviewed.
- [ ] Timeouts, retries, recovery paths, deferred work specified.
- [ ] Logs, metrics, traces, alerts available for critical workflows.
- [ ] Rollback or roll-forward plan exists; rollback owner named.
- [ ] Known issues, user messages, and operational runbooks (or notes) documented.
- [ ] Release owner reviewed the final checklist; monitoring active before users depend on the feature; failure signals known; specs updated to match deployed behavior.

After release: run the post-release review below.

## Part A - Deployment planning

### Environments
An environment is a promise about how carefully code is handled there. Releases move forward only after passing gates. [book ch 23]

| Environment | Purpose | Typical data | Release rule |
|---|---|---|---|
| Local | Build and run while developing | Small fake data | Fast changes allowed |
| Test | Automated checks, verify behavior | Controlled sample data | Only tested changes move forward |
| Production | Real consumers depend on it | Real data | Only reviewed, deployable changes |

Start with these three; list the project's actual environments (add or drop). [book ch 23; adaptation addition]

### Configuration
Configuration = values that change between environments without changing code (connection strings, service URLs, feature flags, log level, limits, provider settings). Hard-coding environment values is a common agent mistake. Define every key before implementation. [book ch 23]

| Config key | Purpose | Example value | Security note |
|---|---|---|---|
| [KEY] | [purpose] | [placeholder] | [secret? never log?] |

Provide a config template file with placeholders only (no real values). [book ch 23, 21] Neutral form of the book's ch23 template (names and keys are examples; real values live only in each environment): [book ch 23, adapted]

```
# config template: placeholders only, committed; real values set per environment
APP_ENV=local                  # local | test | production; not secret
APP_PORT=<port>                # only if the artifact listens on a port
DATABASE_URL=<set_in_environment>   # secret in production
SIGNING_SECRET=<set_in_environment> # signs tokens; never printed in logs
LOG_LEVEL=info                 # avoid debug in production
FEATURE_FLAG_EXAMPLE=false     # feature flags default off
```
Production needs the same keys with production values set outside source (the book adds a base-URL key). For a CLI, library, or pipeline use the same idea: input/output paths, credentials, and run mode come from arguments or environment, not constants in code. [addition]

### Pipeline (single definition) [addition]
The book gives three overlapping pipeline lists (5 stages, 7 prompt items, 8 example steps). Use this one; each stage has a project-specific command and a gate. Skip stages that do not apply (no persisted data -> no Migrate).

| # | Stage | Question | Gate |
|---|---|---|---|
| 1 | Install | Do dependencies resolve? | No missing dependencies |
| 2 | Lint / static checks | Does code meet basic rules? | No blocking errors |
| 3 | Test | Does expected behavior still work? | All required automated checks pass |
| 4 | Build | Can it be packaged for release? | Build completes without errors |
| 5 | Migrate (if persisted data) | Are data changes applied safely? | Tested on realistic data first; reversible or recoverable |
| 6 | Deploy / start | Is the new version running? | Starts with documented config |
| 7 | Smoke test | Does it start and do its core job? | Core path works (probe suited to artifact: health check, CLI run, library import, pipeline dry run) |
| 8 | Deployment verification | Do logs, metrics, permissions look right? | Evidence captured; no critical errors |
| 9 | Release-window monitoring | Any failure signals? | Signals within limits in the plan |
| 5a | Rollback readiness (before stage 6; checked once the draft plan exists, confirmed again here) | Can we return to the last stable version? | Steps and owner written |

Commands live in the plan (e.g. a web service may use its package manager; a CLI, a library, and a data pipeline each differ). [book ch 23; stack-neutralized]

### Containers (only if used)
Specify runtime, port (if any), build command, start command, environment variables, and health-check expectations; do not just say "write a Dockerfile". Build steps must have the dependencies they need at build time; install runtime-only dependencies in the final stage. [book ch 23; build-dependency note is an addition: the book's example installs production-only dependencies before running the build, which is wrong for many projects and is not reproduced here]

### Migrations (only if persisted data exists)
Never treat data-structure changes as ordinary code changes. [book ch 23]

| Question | Why | Spec example |
|---|---|---|
| Reversible? | Rollback harder if not | Provide forward and backward steps |
| Existing data break? | Old records may not fit new rules | Backfill before making a field required |
| Code and data deploy safely together? | Code may expect structure that does not exist yet | Deploy the data change before dependent code |
| Downtime required? | Some changes lock or interrupt | Stage the migration for large datasets |

### Rollback
Must exist before deployment starts. Include: last known stable version; command or manual step to restore it; data rollback rule; health checks and error limits that trigger rollback (project sets numbers); who approves rollback; message to affected users. [book ch 23]

Go/no-go and rollback triggers per stage (adapted from book ch 27): [book ch 27]

| Stage | Action | Gate | Rollback trigger |
|---|---|---|---|
| Prepare | Confirm config and data sources | Config checklist complete | Missing/invalid config |
| Migrate | Apply data changes | Tested on staging data | Migration error or data mismatch |
| Build | Run checks, create package | All required checks pass | Failing check or unsafe warning |
| Release | Deploy with monitoring on | Health checks and core path pass | High error rate or broken core path |
| Verify | Check correctness, permissions, logs | Smoke evidence captured | Data leak risk, wrong output, severe slowdown |

### Deployment checklist (one format)
Status per row: Not started | Ready | Blocked | Done. [book ch 23 statuses plus Done, addition]

| Check | Question | Status |
|---|---|---|
| Requirements | Do released features match approved requirements? | |
| Tests | Do unit, integration, key end-to-end checks pass? | |
| Configuration | Required settings documented? | |
| Secrets | Stored outside source? | |
| Build | Production build succeeds? | |
| Migration | Data changes planned, reversible where possible? | |
| Smoke test | Core path verified after deploy? [addition] | |
| Monitoring | Logs and error checks available after deploy? | |
| Rollback | Path clear before release? | |
| Approval | Release owner and rollback owner named? [addition, from App N] | |

### Agent boundaries for deployment work
Agent produces deployment support files (config template, build/start definitions, smoke script, checklist, rollback note) and does not change application behavior. If a deployment file exposes a missing requirement, update the requirement or deployment task [book ch 23]; record it with `templates/spec-change-record.md` (CHG-###) and do not let the agent improvise [addition].

## Part B - Monitoring, logging, errors, feedback

### What to monitor
Choose signals that matter, not everything. Start with critical workflows and trust-sensitive features. [book ch 24]

| Area | Question | Example signal |
|---|---|---|
| Availability | Can consumers reach it? | Uptime checks, failed requests |
| Correctness | Expected result produced? | Failed workflows, invalid outputs, probes |
| Performance | Fast enough? | Response time, queue delay |
| Errors | What breaks, how often? | Error count/rate, repeated failures |
| Usage | Used as intended? | Feature usage, completion rate |
| Security | Suspicious actions? | Repeated failed sign-ins, permission failures |

Map to the project's type (CLI: exit codes and run duration; data pipeline: freshness and row counts; library: issue reports). [addition]

### Logs
| Type | Use when | Include |
|---|---|---|
| Info | Normal important event | Operation, request ID, status, object ID |
| Warning | Unusual but recovered | Condition, recovery action, affected workflow |
| Error | Workflow fails | Error message, request ID, safe context; stack trace INTERNAL logs only |
| Audit | Sensitive action | Actor, action, target, time, permission result |
| Performance | Task is slow | Duration, operation, threshold exceeded |

[book ch 24; stack-trace scoping addition]

Logs are messages; observability is using logs with metrics, traces, and alerts to understand why. Useful, not noisy. Never log secrets (see 08). [book ch 24]

### Error tracking
Spec defines what counts as an error, what to capture, who reviews, when urgent. [book ch 24]

| Condition | Capture | Severity | Response |
|---|---|---|---|
| Valid credentials rejected | Request ID, actor, step, error class | P1 | Investigate immediately |
| Deferred job fails after timeout | Job ID, duration, timeout | P2 | Check queue, data store, retry logic |
| Unauthorized actor reaches restricted operation | Actor, operation, permission result | P0 | Review authorization rule and security logs |
| External call fails repeatedly | Provider, status, retry count | P2 | Fallback or degrade gracefully |

[book ch 24; the book labels these Critical/High/Medium/Medium, relabeled P0-P3: addition] Use the shared P0-P3 scale (P0 blocks release, data loss, or security; P1 major; P2 minor; P3 cosmetic); the project sets a response time per level. [addition: the book defines no Low level or response times]

### Performance
Do not start with random optimization. Ask: which action is slow, how slow, what is the target, which part is likely responsible. Table: Workflow | Metric | Target | Action if exceeded. Targets are project values, not copied. [book ch 24; numeric rule per conventions]

### Feedback
Treat feedback as engineering input. Map to a requirement, flow, design decision, or missing acceptance criterion. [book ch 24]

| Feedback | Spec action |
|---|---|
| Confusion | Update flow / interface requirement |
| Missing behavior | Add or explicitly reject a requirement |
| Wrong expectation | Clarify roles and access rules |
| Performance complaint | Add performance requirement and monitoring signal |

## Part C - Spec drift

Spec drift = system and spec stop matching: code changed without spec; spec changed without code; or an agent change that works but breaks documented design. A code fix without a spec update solves today's bug and creates tomorrow's confusion. [book ch 24] Every meaningful production lesson should answer: does the spec still describe the system you need to maintain? [book ch 24]

### Drift signals
| Signal | May mean | Do |
|---|---|---|
| Code behavior does not match acceptance criteria | Code changed without spec update, or requirement was wrong | Compare production behavior with requirement; choose source of truth (rule below) |
| Checks pass but users complain | Checks miss real expectation | Update acceptance criteria; add checks |
| Agent suggests out-of-scope changes | Context/task too broad | Narrow task; restate boundaries |
| Bug fix creates new workflow behavior | Fix changed product behavior | Update product spec, technical spec, tests |

[book ch 24]

### Decision rule: spec or code is the source of truth [addition]
1. Find the approved AC/REQ the behavior contradicts. None exists -> the spec has a gap: write it via CHG-### before changing code.
2. Was the behavior intended and accepted (relied on by consumers, approved by an Approver)? Yes -> the spec is wrong: update spec, tests, then docs; code stays.
3. Unintended, or violates a security rule, data safety, or an approved requirement? Yes -> the code is wrong: open BUG-###, add a failing check, fix code; spec stays.
4. Unclear or both sides defensible -> the Approver decides; record the decision (ADR-### if architectural); freeze related changes until decided.
5. Security or data-loss risk: treat the spec as authoritative until the Approver decides.
Always record the outcome in a change record. Never leave spec and code disagreeing after a release.

### Spec drift checklist [book App Q, "Maintenance and Spec Drift Checklist"; the per-signal rows come from the ch24 drift table and are merged into one list: addition]
Status per row: Not started | Ready | Blocked | Done.

After every release [book App Q]:
- [ ] Requirements updated to reflect accepted changes.
- [ ] Interface, data, and technical specs updated if contracts changed.
- [ ] Traceability matrix updated with released test evidence.
- [ ] Architecture decisions that changed direction recorded (ADR).
- [ ] Monitoring observations and known limits added to maintenance notes.

Per drift signal [book ch 24 table]:
- [ ] Code behavior compared with acceptance criteria; source of truth decided (rule above).
- [ ] Complaints compared with check coverage; missing criteria and checks added.
- [ ] Out-of-scope agent suggestions traced to broad context; task narrowed.
- [ ] Bug fixes that changed behavior propagated to product spec, technical spec, tests.

Compare one requirement, one check, and one real behavior for each major area. [book ch 24 exercise, made explicit]

### Monthly maintenance review [book App Q]
- [ ] Top feedback compared with current requirements.
- [ ] Frequent errors reviewed; decide spec or code update.
- [ ] Performance trends and capacity assumptions reviewed.
- [ ] Obsolete tasks removed; superseded decisions marked.
- [ ] Context pack refreshed before handing it to an agent (per-task context pack, `templates/context-pack.md`; refresh any project-level summary too). [book App Q; clarification addition]

### Maintenance checklist (post-release, ongoing) [book ch 24, unified format]
- Key workflows have monitoring requirements.
- Errors grouped and reviewed by severity.
- Logs include request IDs and useful context.
- Performance targets exist for important workflows.
- Feedback mapped to requirements or decisions.
- Specs updated after production behavior changes.
- New or changed behavior has matching checks.
- Agent instructions use the current spec, not outdated context.
- Drift review completed before major changes (also run the drift checklist before any major agent-driven change). [book ch 24 / 24.9]

### Worked example: slow workflow [book ch 24]
1 Read the signal (performance log shows a workflow slower than spec). 2 Compare with spec target. 3 Identify likely cause (e.g. returns all data at once). 4 Update spec (performance and response-size rule) (via CHG-### [addition]). 5 Add checks. 6 Ask the agent for a narrow fix. 7 Review and release; verify after deployment.

Narrow maintenance prompt:
> Use the current technical specification and performance requirement. Change only [component] (book verb: refactor). Do not change authentication, authorization, or unrelated interface responses. Goal: [measurable goal]. Also update or add checks for the new behavior. [book ch 24, neutralized]

## Prompts
Pipeline plan (after requirements, technical spec, and test plan exist):
> Using the requirements, technical specification, and test plan below, create a deployment pipeline. Include install, lint, test, build, migrate (if applicable), deploy, smoke test, deployment verification, release-window monitoring, and rollback readiness. Do not assume any external repository or hosting tool. Keep steps executable from the project folder. [book ch 23, extended]

Deployment support files:
> Using the approved technical specification and deployment checklist, create: (1) a configuration template with placeholders only, (2) build/start definitions if containers are used, (3) a smoke test script, (4) a deployment checklist, (5) a rollback note. Do not change application behavior unless a deployment file exposes a missing requirement; if so, report it [book ch 23]; stop and wait for a change record [addition].

Monitoring requirements:
> You are reviewing the feature specification below. Create monitoring requirements for this feature. Include availability, correctness, performance, error tracking, security, and user-facing impact. Do not invent features outside the specification. Return a table with Signal, Reason, Trigger, and Response. [book ch 24]

Update spec after production feedback:
> You are helping me update a specification after production feedback. Use the current requirement, the observed production behavior, and the proposed change. Return: updated requirement text, affected acceptance criteria, affected tests, affected code areas, and a short change note. Do not add unrelated features. [book ch 24]

Spec drift review:
> Compare the current behavior, tests, user feedback, and monitoring notes against the approved specs. Identify drift, missing documentation, obsolete assumptions, and specs that need revision before the next release. [book App Q]

## AI-feature additions (only if the project has LLM features) [book ch 26, 28]
Review samples and ALL escalated cases before expanding rollout; keep last_updated on each knowledge source and keep the previous approved knowledge base and application version for rollback [book ch 26]; monitor unanswered rate, escalations, confidence, error rates, response time; give users a way to report unclear AI output and update specs, prompts, and checks from it. Thresholds are project values.

## Common mistakes
- Deploying "because the page loads" [book ch 27 deployment rule].
- Fixing code without a change record.
- Copying example numeric targets.
- Logging stack traces to user-visible output.
- No named rollback owner.

## Role notes [addition]
- Requester/PM: owns release goal, feedback triage, spec-versus-code decisions with the Approver.
- Builder: writes config, pipeline, smoke test, rollback note; keeps spec and code aligned.
- Reviewer/QA: runs the checklists and smoke test, verifies evidence before approving release, runs monthly review.

Book inconsistencies resolved here are listed in docs/resolved/07-09.md.
