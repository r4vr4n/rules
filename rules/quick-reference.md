# Quick Reference Card

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
Keyboard Nav:  10-keyboard-navigation.md — tab order, focus, shortcuts, forms, React Flow
Optimal Logic: 10-optimal-logic.md — complexity, memoization, patterns, robust vs hacky
Verify:        Smallest sufficient proof, then STOP
```