---
name: tdd
description: Reference for the red-green loop, seams and mocking rules. Loaded by /implement and the factory builder; not a driver.
user-invocable: false
---

# TDD

- **Seam** = a public boundary where behaviour is observed without reaching inside. Tests live at seams, so internals can be rewritten without touching them. Never write a test at an unagreed seam.
- **One vertical slice at a time.** Write one failing test, add only enough code to pass, repeat. The first cycle is a tracer bullet proving one path end to end. Never write all tests first.
- **Names describe capabilities**, not internals. Renaming an internal function must break nothing.
- **Expected values come from a literal, a worked example or the spec**, never computed the way the code computes them (that is a tautology).
- **Mock only at system boundaries**: external APIs, time, randomness, sometimes filesystem or database. Never mock your own modules.
- **No test runner in the repo, or no independent source of truth for the behaviour**: skip the loop, verify through the acceptance commands, and say so in the output.
- Browser and end-to-end tests are rarely the first seam; prefer the highest non-browser seam.
- If the only available seam is too shallow to exercise the real behaviour, report the missing seam instead of writing a test that gives false confidence.
