### 10.6 State & Data Fetching

**State ownership (FE-STATE-001):**

| State type | Tool |
| --- | --- |
| Server / async data | TanStack Query |
| Shareable / refresh-safe UI | URL search params (TanStack Router) |
| Global ephemeral UI | Zustand |
| Component-local ephemeral | `useState` / `useReducer` |

**Zustand (FE-STATE-002, 003, 006):**

- Slices in `src/store/*Slice.ts`, combined in `useGlobalStore.ts`
- Persist via `partialize` in `useGlobalStore.ts` — never call `localStorage.setItem` directly
- Read with a per-key selector: `useGlobalStore((state) => state.x)`; never bare `useGlobalStore()` destructuring

**URL-owned state (FE-STATE-004):** Default to URL for anything that should survive refresh, be shareable, or participate in history. Read with `getRouteApi(...).useSearch()`, change by navigating. Never keep a parallel `useState` copy. Use `replace: true` for tab/sidebar switches.

**`useEffect` boundaries (FE-STATE-005):** Not for derived state (compute inline or `useMemo`), data fetching (TanStack Query), or setting state in reaction to a prop change. Acceptable for: subscribing/unsubscribing WebSocket/Centrifuge/DOM events; imperative third-party init; `router.navigate(...)` after async result. Always return cleanup. Prefer custom hook in `src/hooks/<feature>/use-*.ts`.