# 16. Domain modeling & ADRs

- Challenge terms against `CONTEXT.md` immediately — prevents vocabulary drift
- Sharpen fuzzy/overloaded terms ("account" = Customer or User?) — precision prevents bugs
- Stress-test with concrete edge-case scenarios — forces boundary clarity
- Cross-reference with code: "you said X, code does Y" — catches implementation drift
- Update `CONTEXT.md` inline when a term resolves — decisions captured while fresh
- `CONTEXT.md` = glossary only, no implementation details — stays stable, doesn't rot
- ADR only when: hard to reverse + surprising without context + real trade-off. Anything less is ADR spam.