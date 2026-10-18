# Tracker: local markdown

No remote needed. Do not mix with another tracker.

## Layout

`.scratch/<feature-slug>/` holds one feature. `spec.md` is the spec. `issues/<NN>-<slug>.md` is one ticket per file, numbered in dependency order. `NN` is the ticket id (`/implement 03`). Each file starts with a front-matter block:

```
---
state: ready-for-agent   # needs-triage | needs-info | ready-for-agent | ready-for-human | wontfix
category: enhancement    # bug | enhancement
labels: [in-factory]     # factory-owned: in-factory, needs-human
blocked_by: [01, 02]
assignee:                # claim target
status: open             # open | closed
---
```

## Operations

- **ensure-labels**: nothing to create. Make `.scratch/` and add it to `.gitignore` only if the user wants tickets uncommitted; otherwise commit it.
- **create-issue**: write the file with the next free `NN`.
- **add-sub-issue**: put the child under the parent's folder (`issues/`).
- **add-blocked-by**: edit `blocked_by` in the front matter.
- **list-ready / frontier**: tickets with `state: ready-for-agent`, `status: open`, empty `assignee`, and every `blocked_by` ticket `status: closed`.
- **claim**: set `assignee` (the session name) and add `in-factory`. Re-read the file right after writing; if `assignee` is not yours, the claim failed.
- **comment**: append under a `## Comments` heading.
- **set-state / close**: edit the front matter.
- **read**: the whole file.
- **selftest**: create two tickets, link, read back, delete.

## Wayfinding operations

- **map** = `.scratch/<slug>/map.md`. **decision** = `.scratch/<slug>/issues/<NN>-<slug>.md` with a `type:` field.
- **map-frontier / resolve**: as above; resolving appends one line to the map's "Decisions so far".
