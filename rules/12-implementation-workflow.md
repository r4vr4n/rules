# 12. Implementation workflow

**Flow:** Grill → Spec → Tickets (vertical slices) → Implement (TDD) → Code-Review → Commit

- **Vertical slices:** each ticket cuts through schema, API, UI, tests — demo-able end-to-end, avoids layer-by-layer integration hell
- **Blockers first:** declare blocking edges, work the frontier — enables parallel work, CI stays green
- **Prefer existing seams;** new seams only at the highest point — fewer seams = less surface = simpler tests
- **Spec template:** Problem → Solution → User Stories → Impl Decisions → Testing Decisions → Out of Scope. Complete context survives context loss.
- **Ticket template:** what to build (user perspective) + Blocked by + Acceptance criteria — self-contained and verifiable
- **Wide refactors = expand → contract:** add new form, migrate in batches, delete old — never breaks CI, blast radius contained
- **Run typechecking often, single tests often, full suite once at end** — fast feedback catches regressions early