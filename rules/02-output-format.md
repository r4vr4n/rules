### 10.7 Router (TanStack Router)

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-ROUTE-001 | File-based routes in `src/routes/`: `__root.tsx` always-rendered root, `$` prefix dynamic segment captured as `params`, `_` prefix pathless layout, `_` suffix un-nests from parent, `-` prefix excluded from the tree, `(folder)` organizational group | 👀 |
| FE-ROUTE-002 | Commit `routeTree.gen.ts` — it's runtime, not a build artifact | 👀 |
| FE-ROUTE-003 | `autoCodeSplitting: true` in the Vite plugin splits non-critical properties automatically; manual splitting via `.lazy.tsx` + `createLazyFileRoute`; don't split the `loader` without a reason | 👀 |
| FE-ROUTE-004 | Error boundaries at layout or route level | 👀 |