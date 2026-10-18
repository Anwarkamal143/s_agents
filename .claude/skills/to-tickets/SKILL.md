---
name: to-tickets
description: Break a spec (or plan) into small, agent-ready tickets with blocking edges and publish them to the tracker after approval. Use after /to-spec. Tickets are what /factory consumes.
disable-model-invocation: true
---

# To tickets

Input: `/to-tickets #<spec>`, a spec in this conversation, or a plan. Operations come from `docs/agents/tracker.md`. If the spec's "Open" section is not empty, stop and send the user back to `/grill`.

If the whole change fits in one context window, skip tickets and use `/implement`.

## Slice

- **Vertical, not horizontal.** Each ticket cuts through every layer it needs (schema, logic, UI, tests) and can be demoed alone. Test: "what can I demo when this is done?" No answer means horizontal: re-slice.
- **One fresh context window** per ticket. If a ticket would need to read most of the repo, split it.
- **Prefactoring first.** If the change is blocked by a messy seam, make that its own first ticket.
- **Wide refactor:** expand (add new form) → migrate in batches (each blocked by expand) → contract (remove old, blocked by all batches).
- **Needs a human** (judgement, access, manual testing)? Label `ready-for-human`, not `ready-for-agent`.

## Present, then approve

Show a numbered list: title, demo, blocked-by, risk tag. Ask the user about granularity, edges, merges and splits. Publish **nothing** until they approve.

## Ticket body

```
## What          (the behaviour, in glossary terms, 2-4 sentences)
## Demo          (what can be shown when done)
## Acceptance    (checkable items; each must be able to FAIL on the base commit)
## Seam          (where the test goes, from the spec)
## Risk          (none | ui | security | one-way: <which door>; used for reviewer routing and PR "Merge Danger")
## Spec          (#<spec>)
```

No file paths or line numbers (they rot while the ticket waits), except snippets from a prototype. Add a `Blocked by: #n` line only when the tracker has no native edges.

## Publish

Blockers first, so their ids exist. For each ticket: `create-issue` (label `enhancement` or `bug`, state `ready-for-agent` or `ready-for-human`), then `add-sub-issue` to the spec, then `add-blocked-by`. Do not run `/triage` over these; they are already agent-ready.

## Done

List the created tickets with their edges and the frontier (tickets with no open blockers). Next step: `/factory next`, or `/implement #<n>` by hand.

Tickets are disposable: if direction changes, delete unfinished ones and keep the spec.
