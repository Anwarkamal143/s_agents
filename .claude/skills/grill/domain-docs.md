# Domain docs: what to write and when

Layout comes from `docs/agents/domain.md`.

## GLOSSARY.md: vocabulary only

Write inline, the moment a term is settled, and only when a vague word has become canonical.

```
**Order**: a customer's confirmed request to buy; exists from checkout until fulfilment. _Avoid_: cart, purchase
```

One or two sentences per term plus `_Avoid_` for rejected synonyms. No implementation details, spec text, scratch notes or general programming terms. If the file grows, trim implementation detail first; split into a `GLOSSARY-MAP.md` only when it is lean and still spans two domains.

## docs/adr/NNNN-slug.md: decisions

Offer to the user; never create silently. Write one only if **all three** hold:

1. Hard to reverse.
2. Surprising without context.
3. The result of a real trade-off between real alternatives.

If one fails, say which and keep the decision in conversation only.

```
# 0007. Store money as integer minor units
Context: ... (one sentence)
Decision: ... (one sentence)
Why: ... (one sentence, name the rejected alternative)
```

Create `docs/adr/` with the first ADR. Number sequentially.
