### Optimal Loops & Logic — Robust Over Hacky

**Algorithm Complexity:**

- Target O(n) or O(log n) where possible. Avoid O(n²) nested loops over large datasets.
- Use built-in methods: `.map()`, `.filter()`, `.reduce()` over manual index-based loops when they express the intent clearly.
- Prefer `for...of` over `for...in` for object iteration (property enumeration vs value iteration).
- Use `Set` for uniqueness checks instead of `.indexOf()` repeated calls: `new Set(array).size` vs `array.filter((v,i) => array.indexOf(v) === i)`.

**Memoization & Derived State:**

- **`useMemo` for expensive derived computation** — Per FE-STYLE-010, measure with React DevTools before adding. Not for every variable, only hot paths.
- **`useCallback` stability** — When passing to memoized children or for hook-dep stability. Not by habit; with React Compiler, hand-written memo is noise.
- **Derive during render** — Instead of storing duplicate state, derive values during render (one source of truth per datum; duplicates drift per FE-12).

**Loop Optimization Patterns:**

| Anti-pattern | Robust alternative |
|---|---|
| `for (let i = 0; i < arr.length; i++)` with push to new array | `[...arr].map(x => x.xform())` or `arr.map(x => x.xform())` |
| `arr.filter(x => { let found; arr.forEach(y => { if (y.id === x.ref) found = y; }); return found; })` | Build a `Map` or `Object` lookup: `const map = Object.fromEntries(arr.map(x => [x.id, x]));` then `arr.filter(x => map[x.ref])` |
| `for...in` on arrays — iterates indices, not values | `for (const v of arr)` or `arr.map(...)` |
| Manual DOM node collection + index tracking | `querySelectorAll` + `Array.from()` + `.map()` |

**State & Fetching:**

- **Parallelize independent fetches** — No request waterfalls. Use `Promise.all` for independent async calls.
- **TanStack Query `select` transformation** — Transform data in query factory rather than in component derived state.
- **Prefetch on hover** — `queryClient.prefetchQuery(queryOptions())` for latency-sensitive data.
- **Virtualized lists** — For 100+ rows, use `@tanstack/react-virtual` instead of rendering all DOM nodes.

**Never premature optimize, but also don't accept the first solution:**

- Measure first — Use React DevTools Profiler, Chrome DevTools Performance panel.
- Profile before optimizing — The "first solution that comes to mind" may be fine for small data volumes.
- Cache results — If the same computation runs multiple times, consider `useMemo` or a reusable selector.
- Avoid N+1 query patterns — Fetch all needed data in one request when possible, or use GraphQL batching.

**Code Quality — Robust vs Hacky:**

- **Delete test** — Apply the deletion test (Rule 14): if removing a module/complexity vanishes, it was a pass-through hack; if it fans out to N callers, it earned its keep.
- **Interface depth** — Depth is an interface property, not implementation. Hide complexity behind a smaller interface.
- **One adapter = hypothetical seam; two = real** — Don't introduce seams unless something actually varies (Rule 14).
- **Type safety as defense** — No `any`, no `as any`, no `@ts-ignore`. Type errors mean the type or code is wrong; fix the one that's wrong (Rule 9).