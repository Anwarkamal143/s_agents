# Factory agent context (shared)

Every factory agent reads this first. Agent files only add what is specific to their role.

## Where you work

Your prompt gives the job folder, the worktree, the branch and sometimes a round or port. The worktree is a checkout of the job branch outside the main checkout you start in. The shell returns to the main checkout after every command: start every Bash command with `cd <worktree> && `, and give Read/Grep/Glob/Edit/Write absolute paths under the worktree. The job folder is in the main checkout. Write only the one file your role names; never edit anything else in the main checkout.

## Read first

1. `AGENTS.md` (or `CLAUDE.md`) and `docs/agents/factory.md` (commands, dev server, one-way doors).
2. `GLOSSARY.md` and the ADRs in `docs/adr/` that touch your area. Use glossary terms; do not rename concepts.
3. Your role's inputs in the job folder (`ticket.md`, `spec.md`, `build.md`, reviews).

## Rules for all

- Lint and format only changed files. Never run a command that rewrites files repo-wide.
- Pages: use the `agent-browser` CLI (`agent-browser skills get core` first) with `--session <job-id>-<role>` on every call; close the session and stop the dev server when done. Do not sign in or submit forms.
- You never spawn other agents.
- First line of your output file is a machine-read verdict or status, exactly as your role specifies.
