### 10.3 Components

| ID | Rule | Tag |
| ---- | ------ | ----- |
| FE-COMP-001 | Shared building blocks in `src/common/`; feature components in `src/components/<feature>/` | 👀 |
| FE-COMP-002 | Split a `.tsx` only past roughly **300 lines** of substantive code — don't preemptively extract leaf components | 🧠 |
| FE-COMP-003 | No intermediate components that exist only to forward props | 👀 |
| FE-COMP-004 | `kebab-case.tsx` file, `PascalCase` default export matching the filename (`data-model-card.tsx` → `DataModelCard`); named exports `PascalCase` for components, `camelCase` for hooks/utils | 👀 |
| FE-COMP-005 | No barrel `index.ts` files — import from the source file | 👀 |
| FE-COMP-006 | Tooltips always via `@frontend/src/common/styled-tooltip.tsx` — no ad-hoc MUI `Tooltip` in feature code | 👀 |
| FE-COMP-007 | User-visible text via `PrimaryText` from `@/common/primary-text` — never a raw `Typography` in feature code; `fontWeight={500}` title / `fontWeight={400}` + `color="text.secondary"` subtitle; never `<strong>` or `fontWeight={600}` | 👀 |
| FE-COMP-011 | Never restate a component's default prop value — no `variant="caption"` on `PrimaryText`, no `width={14} height={14}` on `IconRenderer` | 👀 |
| FE-COMP-008 | `key` is a stable entity id — never an array index | 👀 |
| FE-COMP-009 | Custom boolean props take an `is` / `has` / `should` prefix (`isDisabled`, `hasError`, `shouldShowLabel`); HTML-native booleans passed to DOM elements keep their names (`disabled`, `readOnly`, `checked`) | 👀 |
| FE-COMP-010 | Two style blocks differing only by a constant merge into one shared `sx` factory | 👀 |

**FE-COMP-003 — No prop-drilling scaffolding:** Don't add an intermediate component whose only job is forwarding a long prop list. Instead: React Context or Zustand for data many descendants read, composition (children / render props) so intermediates stay unaware, colocated state, and flatter JSX that lifts the consumer nearer to where the value is produced.

```tsx
// ❌ Intermediate only forwards props
function UserCard({ user, onEdit, onDelete, onView, permissions, theme }) {
  return <UserCardInner user={user} onEdit={onEdit} onDelete={onDelete} onView={onView} permissions={permissions} theme={theme} />
}

// ✅ Composition — parent decides what to render
function UserCard({ user, children }) {
  return <div className="card">{children(user)}</div>
}
// Usage
<UserCard user={user}>
  {(u) => <UserActions user={u} onEdit={...} onDelete={...} />}
</UserCard>

// ✅ Context for data many descendants need
const UserContext = createContext(null)
function UserProvider({ children, user }) {
  return <UserContext.Provider value={user}>{children}</UserContext.Provider>
}
```

**FE-COMP-007 — `PrimaryText`, not `Typography`:** All user-visible text renders through `PrimaryText` (or `EllipsisPrimaryText` when it needs to truncate). Feature code does not import `@teragonia/uikit/Typography`.

```tsx
// ❌
import Typography from "@teragonia/uikit/Typography"
<Typography variant="h6">{concept.name}</Typography>

// ✅
import { PrimaryText } from "@/common/primary-text"
<PrimaryText variant="h6">{concept.name}</PrimaryText>
```

Weights: `fontWeight={500}` for a title, `fontWeight={400}` + `color="text.secondary"` for a subtitle. Never `<strong>`, never `fontWeight={600}`.

**Watch the default when converting.** Bare `<Typography>` renders `body1`; bare `<PrimaryText>` renders `caption`. A `<Typography>` with no `variant` must become `<PrimaryText variant="body1">`, not a bare `<PrimaryText>`, or the text silently shrinks.

**FE-COMP-011 — Default prop values table:**

| Component | Default it supplies | Don't write |
| --- | --- | --- |
| `PrimaryText` / `EllipsisPrimaryText` | `variant="caption"` | `variant="caption"` |
| `IconRenderer` | `width={14} height={14}` | `width={14} height={14}` |
| `StyledTooltip` | `placement="right"` | `placement="right"` |
| uikit `Button` / `PermissionButton` | `color="primary"` | `color="primary"` |

**FE-COMP-008 — Stable keys in lists:**

```tsx
// ✅
{tables.map((table) => <TableRow key={table.id} table={table} />)}
// ✅ composite
key={`${item.entityId}-${item.tableId}`}
// ❌
{tables.map((table, i) => <TableRow key={i} table={table} />)}
```