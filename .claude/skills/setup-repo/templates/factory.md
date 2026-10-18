# Factory config

Repo-specific facts for `/factory` and its agents. Agents read this; none of it lives in agent files.

## Run

- base branch: main
- merge mode: pr            # pr | local
- pr on approve: ready      # ready | draft
- pr on escalate: draft     # draft | none   (a human can review the draft)
- auto-merge: false         # never set true without a human decision
- max resumes: 2

## Commands (run in a fresh worktree)

- setup: TBD
- dev server: TBD            # must accept a port: TBD --port <port>
- test: TBD
- lint (changed files only): TBD
- typecheck: TBD
- build: TBD

## One-way doors (approver always escalates; PR "Merge Danger" names them)

- payments, auth, permissions, database/storage security rules
- deploy, CI or hosting config, and the build scripts they run
- schema migrations, sending email, writing production data
- adding or upgrading a dependency
- deleting or renaming public API, routes or published data
- a spec decision that is really product or business (pricing, public copy, free vs paid)

## Reviewer routing

Run `reviewer-code` always. Run the others only when the ticket's risk tag or the diff matches:

- `reviewer-security`: tag `security`, or diff touches auth, input handling, endpoints, dependencies
- `reviewer-ux`, `reviewer-ui`: tag `ui`, or diff touches user-visible files: TBD (globs)
