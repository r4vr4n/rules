# Gold Standard Rules

1. **Terse Communication** – Respond in short, direct sentences; drop filler, keep technical terms and exact error strings.  This improves readability and reduces token cost while preserving intent.

2. **YAGNI Ladder** – Prioritize existing code, standard library, native platform features, and installed dependencies before writing new code.  Ensures minimal, maintainable additions.

3. **Conventional Commit Messages** – Use `<type>(<scope>): <imperative summary>` with a concise subject and optional body for breaking changes or migrations.  Guarantees clear history and automated tooling compatibility.

4. **Code‑Review Format** – One‑line findings (`L<line>: <problem>. <fix>.`) with optional severity prefixes.  Provides actionable feedback without noise.

5. **File‑Compression Discipline** – Remove only non‑essential prose (articles, filler) while preserving code blocks, URLs, and structural elements.  Keeps files readable for humans and token‑efficient for models.

6. **Auto‑Clarity Rules** – Switch to normal prose when security warnings, irreversible actions, or ambiguous compression occur.  Maintains safety and clarity.

7. **Scope‑Based Subagent Delegation** – Use subagents (investigator, builder, reviewer) only when the task matches their capability, avoiding unnecessary context bloat.

8. **Verification Discipline** – Translate acceptance conditions into the smallest sufficient proof set; stop immediately when proof is complete.  Prevents over‑engineering after correctness is achieved.

9. **Domain‑Model Governance** – Keep a separate `CONTEXT.md` glossary and use ADRs for hard decisions.  Ensures consistent terminology and reversible design changes.

10. **Design‑Time Grilling** – Build a design tree and ask all frontier questions in parallel before implementation.  Eliminates hidden assumptions early.

<!-- 11. JavaScript / TypeScript Best Practices -->
 1. **JS/TS Coding Hygiene** – Prefer `const` over `var`; use strict equality (`===`/`!==`); avoid magic numbers/strings by defining named constants; favor immutability (`toSorted()`, `with()`) over in‑place mutation; use destructuring and rest/spread; enforce `noImplicitAny` and `strict` compiler options; prefer `async/await` with explicit error handling; never use `eval` or build HTML from unsanitized input.

<!-- 12. React & Frontend Practices -->
 1. **React Design Principles** – Components should have a single responsibility; renders must be pure, avoid mutating props/state/refs; use stable keys; follow Hooks rules (top‑level, exhaustive deps); memoize only performance‑critical paths; cleanup timers/subscriptions; avoid side‑effects in render; keep UI logic in separate hooks.

<!-- 13. Test‑Driven Development -->
 1. **TDD Cycle** – Write a failing test first (Red), then minimal implementation (Green), refactor only during code review. Test only at public seams; keep tests isolated from internal implementation; avoid tautological tests.

<!-- 14. Implementation Workflow -->
 1. **Process Flow** – Grill (clarify intent) → Spec (requirements) → Tickets (vertical slices) → TDD (tests) → Review (standards & spec) → Commit. Parallel work on independent tickets; keep CI green.

<!-- 15. Code Review Axes -->
 1. **Review Dimensions** – Separate Standards (style, smell) and Spec (requirement compliance). Use severity prefixes (bug, risk, nit, question). List findings sorted by file and line. Avoid hedging; use `q:` for genuine questions.

<!-- 16. Deep Module Design -->
 1. **Module Design** – Keep interfaces shallow; apply the deletion test to remove pass‑through modules; ensure testable interfaces; use generics, utility types (`Pick`, `Omit`) rather than duplicate interfaces; avoid naming that misrepresents purpose.

<!-- 17. Debugging Discipline -->
 1. **Debug Loop** – Build a tight feedback loop: reproduce, minimize to smallest red scenario, formulate 3‑5 ranked hypotheses, instrument one variable at a time, run regression test before committing. Clean up logs, document hypothesis.

<!-- 18. Domain Modeling & ADRs -->
 1. **Domain Governance** – Maintain `CONTEXT.md` glossary; use ADRs for irreversible or surprising decisions; reference concrete examples; keep definitions close to usage.

<!-- 19. Grilling Decisions -->
 1. **Decision Framework** – Map decisions as a design tree; ask all frontier questions in parallel; differentiate facts (agents find) from decisions (user chooses); finish when frontier is empty.

<!-- 20. Architecture Improvement -->
 1. **Deepening Scan** – Identify shallow modules, apply the deletion test, deepen modules where interface is as complex as implementation, redesign for testability, reduce cross‑module dependencies.

<!-- 21. Research, Triage, Prototyping -->
 1. **Research Workflow** – Delegate to background agents for primary sources; validate claims by reproducing; prototype throw‑away code for single questions; fold prototypes into real code after validation.

<!-- 22. Agent Skills (SKILL.md) -->
 1. **Skill Authoring** – Skill directory must contain `SKILL.md` only; frontmatter: `name`, `description` (≤1024 chars); body: ≤500 lines, core concepts first, minimal examples, anti‑patterns; scripts deterministic, handle errors, no voodoo constants; lifecycle: build after manual solution, trigger tests, version with data/schema changes; review for security.

<!-- 23. Global Overrides -->
 1. **Auto‑Clarity & Boundaries** – Drop terse mode for security warnings, irreversible actions, ambiguous compression, or user clarification requests. Keep language consistent with user input. Artifacts persisted outside chat stay in normal prose; deactivation via explicit commands.

---

**Quick Reference Card**

```markdown
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
Verify:        Smallest sufficient proof, then STOP
```
