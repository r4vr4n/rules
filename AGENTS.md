# AGENTS.md — Consolidated Rules (Summary)

All important, high-signal rules in one file, each with its reasoning. Two layers:

- **Part A — Behavior** (sections 1-8): how an agent communicates, writes, commits, reviews, delegates, verifies.
- **Part B — Craft** (sections 9-20): code quality, TDD, design, debugging, domain modeling, process.
- **Section 21 overrides everything.**

Sources: `must-follow.md` + `CODING-RULES.md` (distilled from 37 engineering skills). **Rule references now point to `rules/` files for detailed guidance.**

---

## Part A — Behavior

**Reference:** `rules/01-communication.md` through `rules/08-verification.md` (removed — consolidated into quick-reference.md)

**Note:** Behavior rules (1-8) are now summarized in `rules/quick-reference.md` with links to original AGENTS.md sections for deep lookup.

---

## Part B — Craft

**Reference:** `rules/ste-rules.md` (STE writing), `rules/10-keyboard-navigation.md` (accessibility), `rules/10-optimal-logic.md` (algorithms/loops), `rules/quick-reference.md` (at-a-glance)

**Key rule references:**

| Rule | File |
|---|---|
| STE writing compliance | `rules/ste-rules.md` |
| Keyboard navigation & shortcuts | `rules/10-keyboard-navigation.md` |
| Optimal loops & logic — robust over hacky | `rules/10-optimal-logic.md` |
| Quick reference card | `rules/quick-reference.md` |

**Detailed rule locations (original AGENTS.md sections, now in `rules/`):**

| AGENTS.md Section | Mapped Rule File |
|---|---|
| 9. JS/TS practices | `rules/09-js-typescript-practices.md` (removed — see `ste-rules.md` for STE) |
| 10.1 Project Layout | `rules/10-react-frontend-practices.md` (removed — see `quick-reference.md`) |
| 10.2 Types | `rules/10-types.md` (removed — covered by `ste-rules.md` word substitutions) |
| 10.3 Components | `rules/10-components.md` (removed) |
| 10.4 Hooks | `rules/10-hooks.md` (removed) |
| 10.5 Forms | `rules/10-forms.md` (removed) |
| 10.6 State & Data Fetching | `rules/10-state-data-fetching.md` (removed) |
| 10.7 Router | `rules/10-router.md` (removed) |
| 10.8 Styling | `rules/10-styling.md` (removed) |
| 10.9 Accessibility | `rules/10-accessibility.md` (removed) |
| 10.10 React Flow | `rules/10-reactflow.md` (removed) |
| 10.11 Architecture | `rules/10-architecture.md` (removed) |
| 10.12 General React | `rules/10-general-react-practices.md` (removed) |
| 10.13 React 19 | `rules/10-react19.md` (removed) |
| 10.14 Mantine | `rules/10-mantine.md` (removed) |
| 10.15 Error Boundaries | `rules/10-error-boundaries.md` (removed) |
| 10.16 Security | `rules/10-security.md` (removed) |
| 10.17 Testing | `rules/10-testing.md` (removed) |
| 10.18 Performance | `rules/10-performance.md` (removed) |
| 10.19 Query+Forms | `rules/10-query-forms.md` (removed) |
| 10.20 Zustand | `rules/10-zustand.md` (removed) |
| 11. TDD | `rules/11-tdd.md` (removed) |
| 12. Implementation Workflow | `rules/12-implementation-workflow.md` (removed) |
| 13. Code Review Two Axes | `rules/13-code-review-two-axes.md` (removed) |
| 14. Deep Module Design | `rules/14-deep-module-design.md` (removed) |
| 15. Debugging | `rules/15-debugging.md` (removed) |
| 16. Domain Modeling | `rules/16-domain-modeling.md` (removed) |
| 17. Grilling Decisions | `rules/17-grilling-decisions.md` (removed) |
| 18. Architecture Improvement | `rules/18-architecture-improvement.md` (removed) |
| 19. Research/Triage/Prototyping | `rules/19-research-triage-prototyping.md` (removed) |
| 20. Agent Skills | `rules/20-agent-skills.md` (removed) |
| 21. Global Overrides | `rules/21-global-overrides.md` (removed) |

**Quick lookup:** Run `rg "Rule ID" rules/` or consult `rules/quick-reference.md` for at-a-glance references.

---

## Quick Reference Card

```
Comm:          Terse, drop filler, never drop negations; full prose outside chat
Minimalism:    YAGNI → reuse → stdlib → native → installed dep → one line → write it
Commit:        type(scope): imperative summary; why over what; body for breaking changes
Review:        L<line>: problem. fix. — separate Standards and Spec axes
TDD:           Red → Green (one slice) → Review (refactor); test at seams only
Implement:     Grill → Spec → Tickets (vertical, blocked) → TDD → Review → Commit
Design:        Deep modules (small interface, lots hidden) at clean seams; deletion test
Debug:         Tight loop → Minimise → 3-5 hypotheses → Instrument one var → Regression test
Domain:        CONTEXT.md glossary + ADRs for hard/reversible/surprising decisions
Grill:         Design tree → frontier rounds → agent finds facts, user decides
Arch:          Scan friction signals → deletion test → deepen shallow modules
Skills:        Solve once manually → extract; what+when in description; test triggers
STE:           Prefer simple direct wording; one term per concept; active voice; 20-word procedures; 25-word descriptions; 3-word noun clusters; explicit articles; WARNING/CAUTION safety
Keyboard:    10-keyboard-navigation.md — tab order, focus, shortcuts, forms, React Flow
Optimal:     10-optimal-logic.md — complexity, memoization, patterns, robust vs hacky
Verify:      Smallest sufficient proof, then STOP
```

**Usage:** Agents should reference `rules/` files for detailed guidance instead of extracting from this consolidated file. AGENTS.md serves as the overview map; `rules/` contains the authoritative rule texts.