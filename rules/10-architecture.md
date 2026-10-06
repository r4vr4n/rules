### 10.11 Architecture & UX (Project-Specific)

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-ENG-001 | No secrets in the bundle; `VITE_*` prefix only for values that may reach the browser | 👀 |
| FE-ENG-002 | `target="_blank"` always with `rel="noopener noreferrer"` | 👀 |
| FE-ENG-003 | Virtualize high-row-count lists; debounce expensive handlers | 🧠 |
| FE-ENG-004 | Route-level code splitting and splitting for heavy widgets | 👀 |
| FE-ENG-005 | Small focused PRs with conventional commit messages | 👀 |
```

### 10.12 General React Practices (Baseline)

**Components**

- Single responsibility — split when a component fetches + manages complex state + renders heavy UI at once
- Pure during render: no mutating props/state/refs, no external writes (also required for React Compiler)
- Colocate state as close to its usage as possible; lift only when sharing is needed
- Derive values during render instead of storing duplicate state — one source of truth per datum; duplicates drift
- Stable unique keys in lists — array index in reorderable lists causes wrong item state

**Hooks**

- Rules of Hooks always: top level only, exhaustive deps; `eslint-plugin-react-hooks` on error, never disabled per-line
- Don't memoize by habit. With React Compiler, hand-written `useMemo`/`useCallback`/`memo()` is noise — memoize only measured hot paths
- `useEffect` synchronizes with external systems ONLY — not for data fetching (use framework loaders / `use()` + Suspense), not for deriving state, not for event handling. Misused effects are the #1 source of double-fetch and stale-data bugs.
- Extract custom hooks for reuse ("would two components want this logic?"), not to hide length
- Subscriptions/timers need cleanup; initialization belongs in lazy state init or module scope, not `useEffect([])`

**State & data fetching**

- Local-first state; global store only for genuinely cross-cutting state
- Immutable updates: `[...prev, item]`, never `push`
- Render loading/error/empty states explicitly; parallelize independent fetches — no request waterfalls

**Architecture & UX**

- Feature-based folders: `features/<name>/{components,hooks,types}` with an explicit public API via `index.ts` — folders-by-type scatters one feature across the tree as apps grow
- Server Components by default in RSC frameworks; Client Components only where interactivity requires; never pass secrets through client props
- `{count && <X/>}` leaks `0` to the DOM — use explicit booleans/ternary
- Accessibility built-in: semantic HTML first, ARIA last resort, keyboard navigable, labeled inputs, alt text
- Performance = SEO (INP/Core Web Vitals): code-split routes, lazy-load below-fold components, dynamic import large dependencies
- Modal/dialog state reflected in URL or server-rendered HTML where possible — deep-linkable and restorable
```