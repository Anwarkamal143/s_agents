---
name: implement
description: Build one decided ticket or spec test-first and commit it. Never reopens the plan. Use by hand as /implement #<n>; the factory builder follows the same steps in factory mode.
disable-model-invocation: true
---

# Implement

Turn decided work into a commit. Do not interview, re-plan or propose another approach. One ticket per session; if it will not fit one context window, say so and send it back to `/to-tickets`.

**Input:** `#<n>` (read it with comments via `docs/agents/tracker.md`), a spec, or a plan agreed earlier in this conversation. **Branch:** the current one; never create or switch branches unless asked. **Factory mode** (builder agent): seams and acceptance come from `spec.md`, nothing is asked interactively, and the orchestrator owns the ticket, review and push.

## Steps

1. **Restate** what you will build in two or three lines, from the ticket's Demo and Acceptance. Identify the seams. By hand: confirm them with the user; in factory mode take them from `spec.md`. Without agreed seams do not start.
2. **Red-green per seam** following `tdd`: one failing test, minimal code, next behaviour. Typecheck often and run single test files as you go.
3. **Verify** every acceptance item, each with its evidence (command and result, or what the page showed). Run the full suite once, at the end.
4. **Commit** short one-line messages, explicit paths, clean tree.
5. **Close out** (by hand only): run `/review-diff` against the base, preferably in a fresh session; tick the ticket's acceptance boxes; `comment` the outcome on the ticket and `close` it only if the user says so, since closing unblocks dependents.

## Done when

Every acceptance item has evidence, the suite and typecheck pass, and the diff is one vertical slice of one ticket. If a criterion cannot be met, stop and say why; do not weaken it.
