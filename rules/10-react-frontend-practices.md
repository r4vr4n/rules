# 10. React & frontend practices

### 10.1 Project Layout & Conventions

**Directory structure (`src/`):**

```
api/queries/use-query-*.ts      # TanStack Query hooks
api/mutations/use-mutate-*.ts   # TanStack mutation hooks
api/helpers/
common/                         # shared building blocks (buttons, tooltips, skeletons)
components/<feature>/           # feature-specific components
constants/                      # global.ts, colors.ts, regex.ts, api-endpoints.ts
hooks/<feature>/use-*.ts        # feature logic
hooks/api/                      # API-wrapping hooks with no feature UI
layouts/                        # persistent shell (nav, drawer)
pages/                          # screen-level components per route
routes/                         # TanStack Router file-based routes
store/*Slice.ts                 # Zustand slices
types/
utils/
```

**File naming:** kebab-case everywhere. `.tsx` for files with JSX, `.ts` for pure logic.

### 10.2 Types (`src/types/`)

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-TYPE-001 | Use `type`, never `interface` | 🤖 |
| FE-TYPE-002 | `T_BASE_*` for API response types, `T_*` for frontend compositions, `T_*_SLICE` for store slices; suffixes `_PAYLOAD` / `_REQUEST` / `_RESPONSE` / `_DETAILS` | 👀 |
| FE-TYPE-003 | Never write an inline type — extract to a named type | 👀 |
| FE-TYPE-004 | Shared across 2+ files → `src/types/`; component-private → same `.tsx`; feature-local → feature `*-types.ts` | 👀 |
| FE-TYPE-005 | Props types are `T_<COMPONENT_NAME>_PROPS`, defined in the same `.tsx` by default; export only when another file imports them | 👀 |
| FE-TYPE-006 | JSDoc on every exported function, component, hook, store action, utility, and named `type` | 👀 |
| FE-TYPE-007 | No cryptic sequential names (`c1`, `c2`, `d1`) — names state the role | 👀 |
| FE-TYPE-008 | Keep TypeScript `strict`; `any` only at a boundary, with immediate narrowing | 🤖 |

**Type naming (FE-TYPE-002 detail):**

| Prefix | Purpose | Example |
| -------- | --------- | --------- |
| `T_BASE_*` | API response types mirroring backend DTOs | `T_BASE_ENTITY`, `T_BASE_DATA_TABLE` |
| `T_*` | Frontend compositions, mutations, payloads | `T_ENTITY_CREATE_PAYLOAD` |
| `T_*_SLICE` | Zustand store slice types | `T_ENTITIES_SLICE` |

| Suffix | Purpose |
| -------- | --------- |
| `*_PAYLOAD` | Request body for a mutation |
| `*_REQUEST` | Full request including discriminator |
| `*_RESPONSE` | API response wrapper |
| `*_DETAILS` | Entity/component detail type |

SCREAMING_SNAKE_CASE after the `T_` prefix. Simple string unions don't need the `T_` prefix but still need JSDoc.

**Why.** `T_BASE_*` marks types you may not freely edit — they mirror a backend DTO, so a change there is a backend contract change.

**FE-TYPE-003 — No inline types:**

```typescript
// ❌
function foo(data: { id: string; name: string }[]) {}

// ✅
type DataItem = { id: string; name: string }
function foo(data: DataItem[]) {}
```

**JSDoc example (FE-TYPE-006):**

```typescript
/**
 * Builds a stable row key for catalog tables.
 *
 * @description Combines entityId and tableId for unique keys across entity switches.
 * @param entityId - Active entity UUID.
 * @param tableId - Catalog table UUID.
 * @returns A string suitable for `key` or `data-id` suffixes.
 * @example
 * const key = buildCatalogRowKey(entity.id, table.id)
 * // => "e12...-t34..."
 */
export function buildCatalogRowKey(entityId: string, tableId: string): string {
  return `${entityId}-${tableId}`
}
```