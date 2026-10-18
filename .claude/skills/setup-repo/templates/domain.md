# Domain docs

- **Layout**: single-context. Terms in `GLOSSARY.md` at the root. Decisions in `docs/adr/NNNN-slug.md`.
- Multi-context repos add a root `GLOSSARY-MAP.md` that lists each context and the path of its own `GLOSSARY.md`; terms go to the context they belong to.
- Created lazily by `/grill`. Nothing is written until a term settles or a decision clears the ADR bar.
- Formats and the ADR bar: `.claude/skills/grill/domain-docs.md`.
