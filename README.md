# spec-driven-engineering

A Claude Code plugin that applies the spec-driven AI engineering method from *Spec-Driven AI Engineering* by Gem Iroko. It works for any role (PM, builder, QA, lead, solo developer) and any project type or tech stack.

**The one rule:** Before code, write the spec. Before accepting code, run the tests. Before release, review the risks. After release, update the spec when reality changes.

## What it does
When you start a project or feature, Claude walks the lifecycle one stage at a time, with a gate before each move:
intent -> requirements and product spec -> technical design -> security and reliability rules -> test plan -> tasks -> implementation -> review -> deploy -> maintain.
It keeps every requirement traceable to a task, a test and code, and updates the specs when behavior changes. Small or solo projects use a lighter "Lite mode".

## Try it without installing
```
claude --plugin-dir <path-to>/spec-driven-engineering
```
Run this in a scratch folder. The plugin is loaded for that session only.

## Install
This folder is both a plugin and a one-plugin marketplace. Put it in a Git repo (this folder as the repo root), then:
```
claude plugin marketplace add <github-user>/<repo>      # or a local path or git URL
claude plugin install spec-driven-engineering@spec-driven-marketplace
```
Update later with `claude plugin update spec-driven-engineering`. Restart Claude Code to apply.

Without Git: zip this folder and run `claude --plugin-dir spec-driven-engineering.zip`.

## Use it
Type `/spec-driven`, or describe the work: "start a new project", "write the test plan", "review this against the spec", "check for spec drift". Claude works one stage at a time and asks you to approve each gate.

## Layout
- `skills/spec-driven/SKILL.md` - core workflow, gates, vocabulary (short; loaded first)
- `skills/spec-driven/references/` - one guide per stage, loaded only when needed (01 intent ... 14 glossary and tools)
- `skills/spec-driven/templates/` - copy-ready artifacts (intent, requirements, PRD, technical spec, ADR, tasks, test spec, handoffs, ...)
- `docs/resolved/` - where the book contradicts itself and how this plugin resolves it

## Source and honesty notes
Content tagged `[book ...]` comes from the book; `[addition]` marks gap fills and generalizations (for example QA guidance, non-web project types, a retrofit path for existing code, Lite mode, and a spec-vs-code decision rule). The book is web/SaaS-centric and contains internal inconsistencies; these are resolved in `docs/resolved/`. The book's figures are images and are not covered. This plugin is an independent study aid and is not affiliated with the author. If you publish it, check that your use of material adapted from the book is permitted.
