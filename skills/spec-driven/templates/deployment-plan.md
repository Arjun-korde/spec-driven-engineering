# Deployment Plan: [Release name]

Lite mode: may merge into docs/spec.md; Approver: self (solo) allowed. [addition]
When: draft at the Technical design stage (config keys, build/release steps, health checks, rollback); finalize at release. CLI/library: replace environments and containers with build, package, distribution and install instructions. [addition]
Delete sections that do not apply (e.g. Migration if no persisted data, Containers if unused). Draft early (before implementation: config keys, health checks, rollback); finalize at release. [book ch 23; pipeline and numbering adapted]

1. Release name:
2. Release goal:
3. Approved requirements included: REQ-###, ...
4. Environments (list the project's real ones)
| Environment | Purpose | Data | Release rule |
|---|---|---|---|
| Local | | | |
| Test | | | |
| Production | | | |
5. Configuration
| Key | Purpose | Placeholder value | Secret? |
|---|---|---|---|
   Config template (placeholders only; real values set per environment):
   APP_ENV=local | test | production   (not secret)
   APP_PORT=<port>   (if the artifact listens)
   DATABASE_URL=<set_in_environment>   (secret in production)
   SIGNING_SECRET=<set_in_environment>   (never printed in logs)
   LOG_LEVEL=info   (avoid debug in production)
   FEATURE_FLAG_EXAMPLE=false
   [book ch 23 template, neutralized; for CLI/library/pipeline: paths, credentials, run mode come from args or environment]
6. Secrets that must not appear in code:
7. Install / lint / test / build commands:
8. Container spec (if used): runtime, port, build command, start command, env vars, health expectations
9. Migration plan (if persisted data): reversible? backfill? deploy order? downtime?
10. Pipeline
| # | Stage | Command | Gate |
|---|---|---|---|
| 1 | Install | | |
| 2 | Lint / static | | |
| 3 | Test | | |
| 4 | Build | | |
| 5 | Migrate | | |
| 5a | Rollback readiness (before stage 6) | | |
| 6 | Deploy / start | | |
| 7 | Smoke test | | |
| 8 | Deployment verification | | |
| 9 | Release-window monitoring | | |
11. Smoke test steps (suited to artifact type):
12. Monitoring checks and failure signals (limits set by project):
13. Rollback: last stable version; restore step; data rollback rule; triggers (checks and error limits); rollback owner; user message
14. Release owner:   Rollback owner:

## Final approval checklist (status: Not started | Ready | Blocked | Done)
| Check | Status |
|---|---|
| Requirements match released behavior | |
| Tests pass (unit, integration, key end-to-end, security, regression) | |
| Configuration documented | |
| Secrets outside source | |
| Build succeeds | |
| Migration planned, reversible where possible | |
| Smoke test ready | |
| Monitoring active before release | |
| Rollback path clear | |
| Specs updated to match deployed behavior | |
