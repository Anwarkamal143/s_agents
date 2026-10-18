---
name: grill
description: Interview the user about an idea or plan until every branch is decided and nothing is assumed. In a repo it records settled terms and hard decisions as it goes. Use for any plan that fits one session.
disable-model-invocation: true
---

# Grill

Stress-test the plan by interview. The user owns every decision; you own every fact.

## Rules

1. **Map the plan as a decision tree.** A round asks the whole frontier: every open decision whose prerequisites are settled. Never put dependent questions in one round.
2. **Facts are yours.** If the code, `GLOSSARY.md`, `docs/adr/` or the web can answer it, look it up (a subagent is fine) instead of asking. Decisions are the user's; never answer your own.
3. **Question format.** Number each question, one line title after `❓`, a short body, then your recommended answer alone on a `➡️` line. If `CLAUDE.md` says "ask one question at a time", do that.
4. **Challenge vocabulary.** When the user's term conflicts with `GLOSSARY.md`, the code, or an earlier answer, stop and resolve it before moving on.
5. **Write as you settle**, only in a repo, only per `domain-docs.md`: a term goes to `GLOSSARY.md` the moment it is settled; offer an ADR only when all three ADR tests pass. Outside a repo write nothing.
6. **Too big?** More than ~3 rounds of new branches, or branches that need research or something to look at, means wrong tool: say so and propose `/wayfinder`, `/research` or `/prototype`.
7. **Never implement.** No code, no spec, no tickets in this skill.

## Done

The frontier is empty. Restate the shared understanding in plain words (decisions, terms, out of scope) and wait for the user to confirm. Then name the next step: `/to-spec` in this same conversation (do not clear first), or `/implement` if the change is small enough for one session.
