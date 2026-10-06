### 10.8 Styling & Code Quality

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-STYLE-001 | No `styled()`. Box system props first, `sx` for nested selectors/theme callbacks, shared `sx` helpers in `src/utils/` | 👀 |
| FE-STYLE-002 | Colors in `src/styles/colors.css` (`:root`), mirrored in `src/constants/colors.ts` | 👀 |
| FE-STYLE-003 | App-wide primitive scalars → `src/constants/global.ts`, regex → `src/constants/regex.ts` with JSDoc. Grouped string literals: declare each as `export const NAME = "VALUE" as const`, then aggregate. Same literal in 4+ places → extract SCREAMING_SNAKE_CASE `export const`; ≤3 → inline is fine | 🧠 |
| FE-STYLE-004 | Template literals, never `+` concatenation | 👀 |
| FE-STYLE-005 | No nested ternary chains — use if/else or a lookup record | 👀 |
| FE-STYLE-006 | No inline `import()` type expressions — import at the top of the file | 👀 |
| FE-STYLE-007 | Named handler functions with `data-*` attributes over inline arrow functions in `onClick` / `onChange` | 👀 |
| FE-STYLE-008 | Import order: React → external (alphabetical) → internal `@/` → relative → `import type` last, blank line between groups | 🤖 |
| FE-STYLE-009 | Display API ISO timestamps via `formatTimestampForDisplay` from `@/utils/format-timestamp-for-display`, using the `T_TIME_READABLE` fields `absolute` and `fromNow` — never raw `dayjs` for display transforms | 👀 |
| FE-STYLE-010 | Measure with React DevTools before adding `useMemo` / `useCallback`; `useMemo` for expensive derived computation, not every variable. `useCallback` when passing to memoized children or for hook-dep stability. `React.memo` not currently used here | 🧠 |
| FE-STYLE-011 | pnpm for every package operation | 👀 |
| FE-STYLE-012 | Presentational-only transforms (casing, truncation) via CSS, never a JS string transform | 👀 |
| FE-STYLE-013 | No inline `style`/`sx` rotate transforms — use the `.rotate-*` classes in `global.css` (add a new one if the needed angle is missing) | 👀 |
| FE-STYLE-014 | Styling the component exposes as a system prop (`fontWeight`, `color`, `display`, `p`, `width`, `borderRadius`, …) is passed as a **prop**; `sx` reserved for CSS with no prop equivalent (`cursor`, `transition`, `opacity`, nested selectors) — log missing prop support in `UIKIT_COMPONENT_GAPS.md` | 👀 |
| FE-STYLE-015 | No stray semicolon inside a call's argument list — `.map(;(x) => …)`, `fn(cb, ;async () => …)`; run `pnpm lint` and `pnpm tsc -b` before committing | 👀 |
| FE-STYLE-016 | Conditional rendering with no else branch → `condition && <Jsx />`; never `condition ? <Jsx /> : null` | 👀 |