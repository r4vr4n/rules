# 11. Test-Driven Development

**Red → Green → Refactor** — but refactor happens at _code-review_, not inside the loop. Keeps the loop fast; review catches smells separately.

- **Write the failing test first** — prevents implementing imagined behavior
- **One vertical slice per cycle** (one seam, one test, minimal impl) — each cycle teaches the next
- **Test only at pre-agreed seams** (public interfaces) — tests survive refactors; tests coupled to internals break on every internal change
- **No horizontal slicing** (all tests first, then impl) — leads to tautological tests for imagined APIs
- **Expected values from an independent source** (literals, spec, worked example) — `expect(add(a,b)).toBe(a+b)` passes by construction and proves nothing

**Anti-patterns to kill:** implementation-coupled tests (mocking internals, testing private methods), tautological tests, testing details instead of behavior.