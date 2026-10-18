---
name: triage
description: Sort incoming issues (bug reports, requests, factory follow-ups) into one category and one state, verify the claim, and write an agent brief when ready. Not for tickets from /to-tickets.
disable-model-invocation: true
---

# Triage

Operations and label names come from `docs/agents/tracker.md`. Every item ends with exactly one category (`bug` | `enhancement`) and one state:

| State | Meaning |
|---|---|
| `needs-triage` | not yet evaluated |
| `needs-info` | waiting on the reporter; say exactly what you need |
| `ready-for-agent` | fully specified, has an agent brief |
| `ready-for-human` | briefed, but needs judgement, access or manual testing |
| `wontfix` | closed with a recorded reason |

## Per item

1. `read` the issue **and its comments**.
2. **Check `.out-of-scope/`** (read all files; match by concept, not keyword). On a match, show the old decision and ask whether it still stands.
3. **Verify.** Bugs: reproduce from the reporter's steps (outcome: confirmed / failed to reproduce / not enough detail). Search the tracker for duplicates by concept.
4. **Recommend** category + state with reasons. **Wait for approval** before changing anything.
5. **Apply** with `set-state`.

## wontfix

- Already implemented: comment pointing to it. No file.
- Rejected bug: polite explanation, close `not_planned`.
- Rejected enhancement: write `.out-of-scope/<slug>.md` (the idea, why not, date), link it in the closing comment, close.

## Agent brief (comment, on `ready-for-agent`)

Durable contract an agent can build from: current behaviour, desired behaviour, interfaces/types/signatures it must satisfy, acceptance that can fail, out of scope. No file paths or line numbers. Add a `Risk:` line as in `/to-tickets`.
