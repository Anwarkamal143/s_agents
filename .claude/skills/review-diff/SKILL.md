---
name: review-diff
description: Review a committed diff on two separate axes, Standards (built right?) and Spec (the right thing?), with a citation for every finding. Use by hand as /review-diff <fixed-point>; the factory reviewer-code follows it. Named to avoid the built-in /code-review.
disable-model-invocation: true
---

# Review diff

Review `git diff <fixed-point>...HEAD` (merge-base; staged and uncommitted changes are excluded, so commit first). Ask for the fixed point if none is given. Before reviewing, check the ref resolves and the diff is non-empty. Review in a fresh session when possible: the session that wrote the code shares its blind spots. **Never spawn agents.**

## Standards: is it built right?

Source of truth, in order: the repo's own rules (`AGENTS.md`, `CODING_STANDARDS.md`, `CONTRIBUTING.md`, docs it points to). Repo rules override everything below. If the repo documents nothing, fall back to these heuristics, never as hard violations: long function, duplicated logic, speculative generality (unused options, fallbacks), a pass-through module that hides nothing, one change forcing edits in many places, comments that restate code. Skip what the linter already enforces.

Every finding cites a repo rule (file and line) **or** a named smell with the quoted hunk, plus the fix wanted.

## Spec: is it the right thing?

Find the spec: the path you were given, an issue reference in commit messages, a matching file under `docs/`, `specs/` or `.scratch/`, else ask. In the factory it is `spec.md` and the ticket's acceptance. Report requirements that are **missing**, **partial**, **implemented wrongly**, and **scope creep**. Every finding quotes the spec line. No spec means "no spec available"; never invent requirements.

## Output

Two blocks, `## Standards` and `## Spec`, never merged. End with the worst finding on each axis and no overall winner. Findings are hypotheses: open the cited file before acting on one. Act on cited-rule findings; do not loop until the review comes back clean.
