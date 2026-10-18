---
name: pr
description: The shape of a pull request body (Summary visual, Evidence, Merge Danger). Reference used by the factory approver and orchestrator and whenever writing a PR body.
user-invocable: false
---

# PR body

A format, not a workflow: it does not push or open anything. Written after review, so it describes a reviewed diff. The body starts at `## Summary`, with no preamble. The reviewer already has the diff open, so show the change, do not narrate it.

## Summary

One to three small visuals, each beside a line of supporting text: the smallest view that makes the point (pseudocode, call tree, component or file tree, a Mermaid diagram, a shaped diff). Keep only the calls, files and boundaries a reviewer needs. Link the ticket and spec.

## Evidence

A real before and after. Visual change: a screenshot if the environment can capture one. Otherwise the exact test that failed and now passes (as pseudocode) or the output that changed. Factory: draw it from `build.md`. "Tests pass" alone is not evidence.

## Merge Danger

Lead with the door: **one-way** (hard to reverse) or **two-way** (cheap to revert), then the blast radius: what breaks if this is wrong. In the factory take it from the approver's `## Door`; do not restate the builder's confidence. Reviewers skim two-way doors with small radius and read one-way doors slowly.

## Rules

- Last lines of a factory PR: `Closes #<ticket>` and the reviewer verdict row, e.g. `Reviews: code PASS · security PASS · ux skipped · ui skipped`.
- A repo PR template, if any, is reconciled in `AGENTS.md`; do not invent competing sections.
- Large diffs make large bodies; this skill does not split a PR.
- Mermaid renders on GitHub/GitLab, not in a terminal.
