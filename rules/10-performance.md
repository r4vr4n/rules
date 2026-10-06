### 10.18 Performance & Bundle Optimization

**Code splitting (TanStack Router):**

```typescript
// vite.config.ts
import { tanstackRouter } from '@tanstack/router-plugin/vite';

export default defineConfig({
  plugins: [
    tanstackRouter({ autoCodeSplitting: true }),
    react(),
  ],
});
```

Splits: `component`, `errorComponent`, `pendingComponent`, `notFoundComponent` per route.

**Manual lazy routes (heavy widgets):**

```tsx
// src/routes/admin.analytics.lazy.tsx
import { createLazyFileRoute } from '@tanstack/react-router';
export const Route = createLazyFileRoute('/admin/analytics')({
  component: () => import('./admin-analytics'),
  pendingComponent: () => <div data-id="admin-analytics-skeleton">Loading analytics...</div>,
});
```

**Virtualized lists (100+ rows):**

```bash
pnpm add @tanstack/react-virtual
```

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';

function VirtualList({ items }) {
  const parentRef = useRef<HTMLDivElement>(null);
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 40,
    overscan: 5,
  });

  return (
    <div ref={parentRef} style={{ height: 400, overflow: 'auto' }}>
      <div style={{ height: virtualizer.getTotalSize() }}>
        {virtualizer.getVirtualItems().map((virtualRow) => (
          <div
            key={virtualRow.key}
            data-id={`list-row-${items[virtualRow.index].id}`}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: `${virtualRow.size}px`,
              transform: `translateY(${virtualRow.start}px)`,
            }}
          >
            {items[virtualRow.index].name}
          </div>
        ))}
      </div>
    </div>
  );
}
```

**React Compiler:** Trust it. No manual `useMemo`/`useCallback`/`React.memo` unless DevTools proves a bottleneck.

**Bundle analysis:**

```bash
pnpm add -D webpack-bundle-analyzer
# package.json script: "analyze": "vite build --mode analyze && npx webpack-bundle-analyzer dist/stats.html"
```

**TanStack Query optimization:**

- `staleTime: 5000` default (FE-API-006)
- `refetchIntervalInBackground: false`
- Use `select` to transform data in query factory
- Prefetch on hover: `queryClient.prefetchQuery(queryOptions())`
```