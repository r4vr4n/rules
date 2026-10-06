# 5. Code review

**Rule:** One line per finding — location, problem, fix. No throat-clearing.

**Format:** `L<line>: <problem>. <fix>.` Prefix `<file>:L<line>:` on multi-file diffs. Sort file→line ascending; zero findings → `No issues.`

**Keep:** exact line numbers, exact symbols in backticks, concrete fix, and the why when it's not obvious.

**Severity prefixes:**

- 🔴 bug — broken behavior, will cause an incident
- 🟡 risk — works but fragile (race, missing null check, swallowed error)
- 🔵 nit — style/naming, author can ignore
- ❓ q — genuine question, not a suggestion

**Why:** severity lets the author triage in seconds; hedging ("it seems like...") hides uncertainty — use `q:` instead.

**Complexity-hunt mode** (diff-only): tag findings `delete:` / `stdlib:` / `native:` / `yagni:` / `shrink:`, end with `net: -N lines`. Correctness/security/perf out of scope in this mode; never flag the required smoke test.

**Boundaries:** reviews only — no fixes, no approve/request-changes, no big-refactor proposals, formatting nits skipped unless meaning-changing. Don't guess — if unsure of intent, reference the line and ask (`q:`).