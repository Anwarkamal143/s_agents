---
name: to-spec
description: Turn decisions already made (this conversation or a finished wayfinder map) into one written spec on the tracker. No interview. Use after /grill or /wayfinder when the build is bigger than one session.
disable-model-invocation: true
---

# To spec

The spec is a **snapshot of decisions already made**, in the project's own vocabulary. Anything it states that the user never decided is a defect. Operations come from `docs/agents/tracker.md`.

## Steps

1. **Gather.** The conversation, or `/to-spec #<map>` (read the map and every resolved child, with comments), plus the codebase, `GLOSSARY.md` and `docs/adr/`. Search the tracker for overlapping issues and link them.
2. **Do not interview.** If something needed is undecided, list it under "Open" and tell the user to resolve it (`/grill`) before tickets. Do not fill it with a guess.
3. **Seams first.** Sketch where the behaviour will be observed by tests: prefer existing seams and the highest one that works, ideally one per change. Show them to the user and get a yes before writing.
4. **Write** the spec (template below) and publish it with `create-issue`, label `spec`. Never label it `ready-for-agent`: only tickets are buildable, and a ready spec invites an agent to build all of it.

## Template

```
## Goal            (one paragraph: what and why, in glossary terms)
## Decisions       (each with its reason; link ADRs and map tickets)
## Seams           (where tests observe behaviour)
## Requirements    (numbered, testable statements)
## Acceptance      (observable behaviours; each says how to verify: command, page, output)
## Out of scope    (what a reasonable builder might wrongly add)
## Open            (undecided items; must be empty before /to-tickets)
```

No file paths or line numbers: the code will move before this is built. Refactors use Decisions and Seams, and record the big choices as ADRs through `/grill`.

## Done

Show the issue link. Tell the user to run `/to-tickets` **in this same conversation**; clearing or compacting first risks a truncated spec. Tell them to read Seams and Out of scope closely: errors are cheapest to catch there.
