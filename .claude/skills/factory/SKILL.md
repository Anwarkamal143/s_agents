---
name: factory
description: Run the software factory on one ticket. Use for /factory #<n>, /factory next, /factory "<small change>", /factory approve <job-id>, /factory rework <job-id> <note>. The session becomes the orchestrator of the ticket → spec → build → review → approve → PR loop.
---

# Factory

You orchestrate one job. You spawn every agent and are the only writer of `job.json` and `ticket.md`. You never write code, specs, reviews or decisions. Agents write one file each; you move the job along.

Inputs the factory trusts, created by prep: tickets on the tracker (`/to-tickets`), config in `docs/agents/tracker.md` and `docs/agents/factory.md`. If either doc is missing, stop and say `/setup-repo`.

```
ticket → spec-writer → builder → [code always; security, ux, ui by routing] → WAIT FOR ALL
                         ▲                                                      │
                         └──────── any CHANGES: resume builder ◄────────────────┤
                                                                                ▼ all PASS
                                       PR (ready) ◄─ APPROVE ─ approver ─ ESCALATE → draft PR + needs-human
```

| Step | Agent | Writes |
|---|---|---|
| spec | `spec-writer` | `spec.md` (or `NEEDS-INFO`) |
| build | `builder` | `build.md` + commits on `factory/<job-id>` |
| review | `reviewer-code`, `reviewer-security`, `reviewer-ux`, `reviewer-ui` | `round-N/review-<lens>.md` |
| approve | `approver` | `decision.md` |

Shared agent context (worktree rules, what to read) is in `context.md`. Repo facts (base branch, commands, one-way doors, routing, PR settings) are in `docs/agents/factory.md`. Read both factory docs and `tracker.md` before starting; nothing repo-specific lives in agent files.

## Files

`<repo>` = main checkout. `factory/jobs/` is gitignored runtime state.

```
<repo>/factory/jobs/<job-id>/job.json
<repo>/factory/jobs/<job-id>/ticket.md        ticket + comments + parent spec, snapshot by you
<repo>/factory/jobs/<job-id>/spec.md  build.md  decision.md  rework-<k>.md  pr.md
<repo>/factory/jobs/<job-id>/round-<N>/review-{code,security,ux,ui}.md
<repo>/../<name>-factory/<job-id>/            the job's worktree
```

```json
{ "id": "012-add-login", "ticket": 12, "spec": 9, "title": "Add login",
  "branch": "factory/012-add-login", "worktree": "/abs/path",
  "stage": "review", "round": 2, "risk": ["security"], "pr": null,
  "reviews": { "code": "PASS", "security": "CHANGES", "ux": "SKIPPED", "ui": "SKIPPED" } }
```

`stage`: `spec | build | review | approve | pr | merged | needs-human`. `reviews` values: `pending | PASS | CHANGES | SKIPPED`. Keep all four keys so the dashboard schema is stable. Rewrite the whole file on every change. Job id = `<3-digit ticket number>-<2-4 word slug>`.

## Prompts

Every agent prompt starts: `Job folder: <repo>/factory/jobs/<job-id>/. Worktree: <worktree>. Branch: factory/<job-id>.` Agents that open pages get a port: `5100 + 10n` builder, `+1` ux, `+2` ui, with `n` the ticket number mod 100, as ` Port: <port>.`

## /factory #<n>  (and /factory next)

`next`: run the tracker's **frontier**, take the lowest ticket number. None: say so and stop. A free-text `/factory "<text>"`: draft a ticket (What / Demo / Acceptance / Risk), show it, and only after the user says yes `create-issue` it as `enhancement` + `ready-for-agent`; then continue with its number.

1. **Claim.** `claim` the ticket. If it fails, someone has it: pick another or stop. The ticket must be `ready-for-agent` and have no open blockers.
2. **Snapshot.** Create the job folder (`mkdir`, no `-p`; on collision stop). `read` the ticket with all comments and its parent spec; write `ticket.md`. Extract the `Risk:` line into `job.json.risk`.
3. **Worktree.** `git worktree add -b factory/<job-id> <worktree> <base>` then run `setup` from `factory.md` inside it. On failure: release the claim (remove `in-factory`), tell the user, stop.
4. **Spec.** Spawn `spec-writer`: `<paths> Ticket: #<n>.` Read the first line of `spec.md`:
   - `NEEDS-INFO`: `comment` the questions on the ticket, `set-state needs-info`, remove `in-factory`, `stage: needs-human`, stop.
   - otherwise continue.
5. **Build.** `stage: build`. Spawn `builder` **named `builder-<job-id>`**: `<paths> Build round 1. Port: <port>.` Then **Build check**.

### Build check

First line of `build.md`: `STATUS: READY` → review round. `STATUS: BLOCKED` → **Needs human**.

### Review round N

1. `stage: review`, `round: N`. Decide the lenses: `code` always; `security`, `ux`, `ui` per the routing rules in `factory.md` (risk tags first, then the diff against the base). Set the rest to `SKIPPED`, the chosen to `pending`. Create `round-<N>/`.
2. Spawn the chosen reviewers **in one message**: `<paths> Review round N.` (+ port).
3. **WAIT FOR ALL.** On each finish, read the first line of its file (`VERDICT: PASS|CHANGES`), write it to `job.json` at once. The round is done only when every chosen file exists. Never resume the builder mid-round. A missing file or malformed first line: stop and tell the user.
4. Decide: all PASS → **Approve**. Any CHANGES with resumes left (`max resumes` in `factory.md`) → `stage: build`, `SendMessage` to `builder-<job-id>`: `Round N reviews are in factory/jobs/<job-id>/round-<N>/. Address every CHANGES finding, commit, update build.md for round N+1.` then Build check. No resumes left → write `decision.md` as `ESCALATE` + `Review rounds exhausted. Outstanding CHANGES from: <lenses>.`, then **Escalate**.

### Approve

`stage: approve`. Spawn `approver`: `<paths> Final round: N.` First line of `decision.md`: `APPROVE` → **Deliver**; `ESCALATE` → **Escalate**.

### Deliver (APPROVE)

`pr` mode (default):
1. `git -C <worktree> push -u origin factory/<job-id>`. Push this branch only; never the base branch.
2. Compose `pr.md` using the `pr` skill from `spec.md`, `build.md`, `decision.md` (Summary visual, Evidence, Merge Danger from the approver's Door section). Last lines: `Closes #<n>`.
3. Open the PR on the tracker host (GitHub: `gh api repos/<o>/<r>/pulls -f title=... -F body=@pr.md -f head=factory/<job-id> -f base=<base> -F draft=<false if "pr on approve: ready">`). Record the URL in `job.json.pr`.
4. `comment` the PR link on the ticket. Keep `in-factory`; merge closes it. Remove the worktree (`git worktree remove`), keep the branch. `stage: pr`.
5. Never merge, never enable auto-merge unless `factory.md` says `auto-merge: true`.

`local` mode: tree must be clean and on the base branch, else `needs-human` with "approved but main checkout busy". Then `git merge --no-ff`, remove worktree and branch, `close` the ticket (`completed`), `stage: merged`. On failure `git merge --abort`, `needs-human`.

Dependents of a ticket become startable when it **closes** (PR merged). Chains therefore wait on a human merge; that is intended.

### Escalate / Needs human

`stage: needs-human`, `set-state`/label `needs-human` on the ticket, `comment` the quoted `decision.md` (or BLOCKED reason). If `pr on escalate: draft`, push and open a draft PR as above so the human can review in the PR; keep the worktree. Tell the user which job waits, the worktree path and PR link.

## /factory approve <job-id>

Job must be `needs-human`. `pr` mode: `gh pr ready <number>` (if blocked, tell the user to click "Ready for review"), remove `needs-human`, `stage: pr`. `local` mode: run the local merge. Humans merge PRs.

## /factory rework <job-id> <note>

Job must be `needs-human`. Write the note verbatim to `rework-<k>.md`, `stage: build`, recreate the worktree from the branch if missing (`git worktree add <worktree> factory/<job-id>`), spawn a fresh `builder-<job-id>`: `<paths> Rework: read rework-<k>.md, address it, update build.md for round N+1. Port: <port>.` Fresh `max resumes`. Continue from Build check; push updates the existing PR.

## Reporting

One short line per transition, e.g. `012-add-login: round 2, security CHANGES, code PASS, ux/ui skipped → resuming builder`. End with merged/PR/needs-human and why.
