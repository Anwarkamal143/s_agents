---
name: wayfinder
description: Plan an effort too big or too unclear for one session. Charts it as a map of decision tickets on the tracker, then resolves one decision per session. Plans only; never builds.
disable-model-invocation: true
---

# Wayfinder

Operations (`create-issue`, `add-sub-issue`, `add-blocked-by`, `map-frontier`, `claim`, `resolve`, ...) are defined in `docs/agents/tracker.md`. Read it first. If it is missing, stop and tell the user to run `/setup-repo`.

## Choose

- One session is enough: use `/grill`.
- Decisions already made: go to `/to-spec`.
- Otherwise continue. Scope the map to **one bounded effort**, never a whole product.

## New map

1. **Destination.** Ask the user what "done planning" looks like. Write it down; every ticket is measured against it.
2. **Breadth-first grill** to surface open questions. If there is no real fog, say the effort is small and hand back to `/grill`.
3. **Chart.** Create the map issue (`wayfinder:map`) with the sections below, then one child per decision.

```
## Destination
## Decisions so far      (one line per closed ticket, linked)
## Not yet specified     (fog: expected decisions you cannot phrase sharply yet)
## Out of scope
## Notes
```

A question becomes a ticket as soon as you can **state it precisely**, even if you cannot answer it. Otherwise it stays in fog; never in both places.

| Type label | Settled by | Mode |
|---|---|---|
| `wayfinder:grilling` (default) | `/grill` in a fresh session | human in the loop |
| `wayfinder:prototype` | `/prototype`, link the branch | human in the loop |
| `wayfinder:research` | `/research`, may run in parallel | agent |
| `wayfinder:task` | manual unblocker (access, data); never implementation | agent or human |

Add blocking edges where one decision depends on another.

## Resolve a ticket (one per session)

1. `map-frontier`; pick an open, unblocked ticket; `claim` it.
2. Work **only** that ticket, by its type. A human-in-the-loop ticket is settled by live exchange; answering your own questions breaks it.
3. `resolve`: comment the resolution, close, add one line to "Decisions so far". Promote any fog that is now sharp. Stop.

## Rules

- **Plan, don't build.** A ticket reading "build X" is mistyped. A `task` that looks like implementation is suspicious: re-type or drop it.
- The map is an index: one line plus a link per decision. Keep detail in tickets.
- Notes are never permission to write production code.
- If a closed decision turns out wrong, tell the user, reopen the affected tickets and update the map. Do not design around it.

## Done

Nothing left to decide: say so, then `/to-spec #<map>` (collapses the decisions into one spec), then `/to-tickets`.
