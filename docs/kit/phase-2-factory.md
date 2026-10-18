# Factory rewiring (phase 2)

Drop `.claude/` over the factory from phase 0 (the original zip) and the prep kit from phase 1. Same file names replace the old ones. Delete `factory/backlog.md`; the queue is now the tracker.

## What changed vs the original factory

| Area | Before | Now |
|---|---|---|
| Queue | `backlog.md` + edit-based claim | tracker **frontier** (`ready-for-agent`, no open blockers) + `claim` by assignment |
| Input | one-line feature | a ticket and its parent spec, snapshotted to `ticket.md` |
| `spec-writer` | invents a spec | grounds the ticket in real code; returns `NEEDS-INFO` instead of deciding product questions |
| `builder` | tests against criteria | `/implement` + `tdd`; bug tickets need a red repro first |
| Reviews | four fixed | `reviewer-code` always (Standards / Spec axes); security, ux, ui routed by ticket risk and diff, others `SKIPPED` |
| `approver` | APPROVE or ESCALATE | adds an independent **Door** (one-way/two-way + blast radius) |
| End of job | local merge | push the branch, open a PR (ready on approve, draft on escalate), `Closes #n`; humans merge |
| Repeated text | "Where you work" copied 7x | `.claude/skills/factory/context.md` |

## Notes and known limits

- `skills:` in agent frontmatter preloads skills into the subagent. Verify it works on your Claude Code version; if not, add "read `.claude/skills/<name>/SKILL.md`" to the agent body.
- Dashboard: `reviews` can now be `SKIPPED` (and jobs have `ticket`, `pr`, stage `pr`). `dashboard.html` is not updated in this kit; treat `SKIPPED` as grey and show `pr` as a link.
- Dependent tickets start only after their blocker **closes** (PR merged), so chains wait on a human merge. Stacked PRs are a possible later option.
- `gh pr ready` uses GraphQL and may be blocked in some sandboxes; the factory then asks the human to click "Ready for review".
- The skill that reviews diffs is `/review-diff`, not `/code-review`, to avoid shadowing the built-in command.
- Not in this kit: `/retro`, `/diagnose`, `/prototype`, `/research`, `/wait-what` (phase 3).
