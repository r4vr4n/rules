**API Layer (TanStack Query):**

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-API-001 | Queries in `api/queries/use-query-*.ts`, mutations in `api/mutations/use-mutate-*.ts` | 👀 |
| FE-API-002 | All paths in `src/constants/api-endpoints.ts` — never hardcode a URL elsewhere | 👀 |
| FE-API-003 | Export a `*QueryOptions()` factory plus a thin hook; the query key is defined **once**, in the factory | 👀 |
| FE-API-004 | Gate parameterized queries with `enabled: !!param` | 👀 |
| FE-API-005 | Mutations always go through `useCustomMutation` from `@/api/helpers/use-custom-mutation`; `successMessage` / `errorMessage` / `invalidateQueryKeys` in `meta` for global handling | 👀 |

**FE-API-005 — Mutation pattern:**

```typescript
export function useCreateSomething() {
  return useCustomMutation({
    url: SOME_ENDPOINTS.CREATE(),
    successMessage: "Created",
    errorMessage: "Failed to create",
    invalidateQueryKeys: [somethingsQueryOptions().queryKey],
  })
}
```

| FE-API-011 | Parameterized mutations export a `*MutationOptions(params)` factory plus a thin hook, mirroring FE-API-003 | 👀 |
| FE-API-006 | `refetchInterval` belongs in the options factory, never in a component; default 5000ms with `refetchIntervalInBackground: false` | 👀 |
| FE-API-007 | Centrifuge only via the `use-canvas-realtime.ts` hook pattern — never instantiated in a component. Never poll **and** subscribe for the same resource | 👀 |
| FE-API-008 | Skeletons for initial load, spinners only for user-triggered actions, always a visible error fallback; use `isPending` | 👀 |
| FE-API-009 | Latency-sensitive mutations (reorder, toggle, delete) use the optimistic `onMutate` / rollback / `onSettled` pattern | 🧠 |
| FE-API-010 | Guard runtime boundaries before use — `?.`, `??`, `Array.isArray`, `isSuccess` | 👀 |

**Query factory example (FE-API-003):**

```typescript
export function somethingsQueryOptions() {
  return generateQueryOptions<T_BASE_SOMETHING[]>({
    queryKey: ["somethings"],
    url: SOME_ENDPOINTS.SOMETHINGS(),
    includeEnvAndEntity: false,
  })
}

export function useQuerySomethings() {
  return useQuery(somethingsQueryOptions())
}
```

**Polling config in factory (FE-API-006):**

```typescript
export function workflowStatusQueryOptions(entityId: string) {
  return generateQueryOptions<T_BASE_WORKFLOW_STATUS>({
    queryKey: ["workflow-status", entityId],
    url: WORKFLOW_ENDPOINTS.STATUS(entityId),
    refetchInterval: 5000,
    refetchIntervalInBackground: false,
  })
}
```

**Loading/error states (FE-API-008):**

```tsx
const { data, isPending, isError } = useQueryCatalogTables(tableId)
if (isPending) return <TableSkeleton />
if (isError) return <ErrorState message="Failed to load tables" />
```

**Optimistic updates (FE-API-009):**

```typescript
return useCustomMutation({
  onMutate: async (newItem) => {
    await queryClient.cancelQueries({ queryKey: itemsQueryOptions().queryKey })
    const previous = queryClient.getQueryData(itemsQueryOptions().queryKey)
    queryClient.setQueryData(itemsQueryOptions().queryKey, (old) => /* apply change */)
    return { previous }
  },
  onError: (_err, _vars, context) => {
    queryClient.setQueryData(itemsQueryOptions().queryKey, context?.previous)
  },
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: itemsQueryOptions().queryKey })
  },
})
```