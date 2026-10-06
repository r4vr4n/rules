### 10.20 Zustand Slices & Middleware

**Slices pattern (FE-STATE-002):**

```typescript
// src/store/entity-slice.ts
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';
import { persist } from 'zustand/middleware';
import { devtools } from 'zustand/middleware';

interface EntityState {
  selectedId: string | null;
  setSelectedId: (id: string | null) => void;
  entities: T_ENTITY[];
  addEntity: (entity: T_ENTITY) => void;
  removeEntity: (id: string) => void;
}

export const createEntitySlice = (set: any, get: any) => ({
  selectedId: null,
  setSelectedId: (id: string | null) => set({ selectedId: id }),
  entities: [],
  addEntity: (entity) => set((state) => ({ entities: [...state.entities, entity] })),
  removeEntity: (id) => set((state) => ({ entities: state.entities.filter((e) => e.id !== id) })),
});

// src/store/ui-slice.ts
interface UIState {
  sidebarOpen: boolean;
  toggleSidebar: () => void;
}

export const createUISlice = (set: any, get: any) => ({
  sidebarOpen: true,
  toggleSidebar: () => set((state) => ({ sidebarOpen: !state.sidebarOpen })),
});
```

**Combine with middleware (order matters):**

```typescript
// src/store/use-global-store.ts
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';
import { persist } from 'zustand/middleware';
import { immer } from 'zustand/middleware/immer';
import { createEntitySlice } from './entity-slice';
import { createUISlice } from './ui-slice';

export const useGlobalStore = create(
  devtools(
    persist(
      immer((...a) => ({
        ...createEntitySlice(...a),
        ...createUISlice(...a),
      })),
      { name: 'global-store', partialize: (state) => ({ entities: state.entities, sidebarOpen: state.sidebarOpen }) }
    ),
    { name: 'GlobalStore' }
  )
);
```

**Selector usage (FE-STATE-006):**

```tsx
// ✅ Per-key selector
const selectedId = useGlobalStore((state) => state.selectedId);
const setSelectedId = useGlobalStore((state) => state.setSelectedId);

// ✅ Object selector with useShallow
import { useShallow } from 'zustand/react/shallow';
const entity = useGlobalStore(useShallow((state) => state.entities.find(e => e.id === id)));

// ❌ Never bare destructuring
const { selectedId } = useGlobalStore();
```

**Persist rules:**

- Only `localStorage` via `persist` middleware (never `localStorage.setItem` directly)
- `partialize` to persist only serializable state
- No sensitive data in persisted state

**TypeScript typing:**

```typescript
import { StateCreator } from 'zustand';
import { persist, devtools, immer } from 'zustand/middleware';

type GlobalState = EntityState & UIState;

type Middlewares = [
  ['zustand/immer', never],
  ['zustand/persist', unknown],
  ['zustand/devtools', never]
];

const createGlobalStore: StateCreator<GlobalState, Middlewares, [], GlobalState> = 
  devtools(persist(immer((...a) => ({
    ...createEntitySlice(...a),
    ...createUISlice(...a),
  })), { name: 'global-store' }));
```