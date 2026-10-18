---
name: setup-repo
description: One-time (re-runnable) repo setup. Records the tracker, labels, domain-doc layout and factory config that every other skill reads. Run before /grill, /wayfinder, /to-spec, /to-tickets, /triage or /factory.
disable-model-invocation: true
---

# Setup repo

Record how this repo works, in markdown, so the other skills stay identical across projects. Skills read `docs/agents/*` at run time; they never hard-code a tracker.

## 1. Explore (silent)

Read `git remote -v`, `AGENTS.md`/`CLAUDE.md`, package manifests, CI config, and any existing `docs/agents/`. Skip every question these already answer. Re-runs only diff against what exists.

## 2. Propose, then ask once

Lead each item with your recommendation. Ask all open items in a single round.

1. **Tracker**: `github` (default when the remote is GitHub), `gitlab`, `local`, or `other` (user describes the workflow in a paragraph).
2. **Merge mode**: `pr` (default: approved jobs open a PR, a human merges) or `local` (orchestrator merges into the base branch).
3. **Commands**: install/setup, dev server (with port flag), test, lint, typecheck, build. Detect from manifests; mark unknowns `TBD`.
4. **One-way doors**: changes the approver must always escalate. Propose the defaults in `templates/factory.md` and let the user edit.
5. **Domain layout**: single `GLOSSARY.md` + `docs/adr/` (default); offer `GLOSSARY-MAP.md` only on monorepo signals.

Write nothing until the user confirms.

## 3. Write

- `docs/agents/tracker.md` from `templates/tracker-<choice>.md`.
- `docs/agents/domain.md` from `templates/domain.md`.
- `docs/agents/factory.md` from `templates/factory.md`, filled with the confirmed values.
- `AGENTS.md`: create a skeleton if missing (commands, dev server, validation, conventions); then add an `## Agent skills` block that points at the three files above, one line each. If only `CLAUDE.md` exists, add the block there. When creating fresh, make `CLAUDE.md` contain just `@AGENTS.md`.
- `.gitignore`: add `factory/jobs/` and `.scratch/`.
- Create `.out-of-scope/.gitkeep` only if the tracker is not `local`.

## 4. Create the labels

Run the tracker file's `ensure-labels` operation. Labels are never created by the other skills, so a missing label fails later.

## 5. Verify, then report

Check: the three docs exist, the block is in the instruction file, `ensure-labels` succeeded and the tracker CLI is authenticated. Report what was written and anything left `TBD`. Stop. Do not start planning.
