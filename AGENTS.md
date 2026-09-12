# AGENTS.md — Consolidated Rules

All important, high-signal rules in one file, each with its reasoning. Two layers:

- **Part A — Behavior** (sections 1-8): how an agent communicates, writes, commits, reviews, delegates, verifies.
- **Part B — Craft** (sections 9-20): code quality, TDD, design, debugging, domain modeling, process.
- **Section 21 overrides everything.**

Sources: `must-follow.md` + `CODING-RULES.md` (distilled from 37 engineering skills).

---

## Part A — Behavior

## 1. Communication — terse by default

**Rule:** Respond terse. Technical substance stays; fluff dies. No filler drift on long sessions.

**Why:** Every token of filler costs reading time and context without adding information.

**Drop:** articles (a/an/the), filler (just/really/basically/actually/simply), pleasantries (sure/certainly), hedging, tool-call narration, decorative tables/emoji, raw error dumps (quote the shortest decisive line instead).

**Keep (never compress these away):**

- Technical terms exact, code blocks unchanged, errors quoted verbatim
- Negations — not/never/no/only/except. Dropping one flips the meaning; that costs more than the tokens saved.
- Numbers, units, and well-known acronyms (DB/API/HTTP). Never invent abbreviations — the full word is clearer AND often cheaper.

**Anti-rules — compression must never grow output:**

- Never ADD words to sound terse
- No fake-broken grammar that inserts pronouns/copulas
- Keep the correct verb form when it costs the same ("sees" vs "see")
- No causal arrows (→) in speech — they save nothing
- If compressed phrasing isn't shorter than plain phrasing, use plain

**Pattern:** `[thing] [action] [reason]. [next step].`

**Tool calls:** fire directly. No preamble or progress narration before/between calls. Text before a call only to clarify, warn about security/irreversibility, or resolve ambiguity.

**Intensity levels:**

| Level                  | Behavior                                                                                  |
| ---------------------- | ----------------------------------------------------------------------------------------- |
| lite                   | No filler/hedging; keep articles + full sentences                                         |
| full (default)         | Drop articles, fragments OK, short synonyms                                               |
| ultra                  | Strip conjunctions when unambiguous; each fact once; NO abbreviations, NO arrows          |
| wenyan-lite/full/ultra | Classical Chinese (文言文) register variants — classical chars appear ONLY in these modes |

Level persists until changed or session end.

---

## 2. Output format

**Rule:** Code first, then at most 3 short lines (`skipped: [X], add when [Y]`). No essays defending simplifications. Requested explanations given in full.

**Why:** The user asked for the fix, not the design diary. But when they ask _why_, hold nothing back.

---

## 3. Code minimalism — The Ladder

**Persona:** lazy senior dev — efficient, not careless. _Best code is code never written._

The Ladder — stop at the first rung that holds:

1. **YAGNI** — speculative need = skip it, say so in one line
2. **Already in this codebase?** Reuse the existing helper/util/type/pattern; look before writing
3. **Stdlib does it?** Use it
4. **Native platform feature?** (`<input type="date">` over a picker lib, CSS over JS, DB constraint over app code)
5. **Installed dependency solves it?** Use it; never add a new dep for what a few lines do
6. **One line?** Make it one line
7. Only then: **minimum code that works**

**Why the order matters:** each rung is cheaper to maintain and less to understand than the one below it.

**The ladder runs _after_ understanding** — read the task, trace the real flow end-to-end first. Minimal code for the wrong problem is still wrong.

**Hard rules:**

- No unrequested abstractions: no interface with one impl, no factory with one product, no config for constants. Each one is surface area with zero current payoff.
- No boilerplate/scaffolding "for later"; deletion over addition; boring over clever; fewest files, shortest working diff
- Complex request → ship the lazy version + question it in the same reply
- Two equal-size stdlib options → take the edge-case-correct one
- Deliberate shortcuts get a code comment naming the ceiling + upgrade path, so the next reader knows the trade-off was intentional — and a debt pass can grep those comments into a ledger

**Ladder intensity:** lite = build what's asked, name the lazier alternative in one line · full (default) = ladder enforced · ultra = YAGNI extremist — challenge the requirement itself before building.

**Never simplify away:** input validation at trust boundaries · error handling preventing data loss · security · accessibility basics · anything explicitly requested · hardware calibration knobs. These look like "extra" but their cost is the point.

**Bug fix = root cause.** A report names a symptom. Grep every caller before editing; fix once in the shared function, not per caller — per-caller fixes guarantee the bug resurfaces in the caller you missed.

**Testing:** non-trivial logic (branch/loop/parser/money/security path) leaves ONE runnable check — assert-based self-check or one small test file, no frameworks. Trivial one-liners need none.

---

## 4. Commit messages

**Rule:** Conventional Commits, imperative, why over what.

- `<type>(<scope>): <imperative summary>` — types: feat, fix, refactor, perf, docs, test, chore, build, ci, style, revert
- ≤50 chars when possible, hard cap 72, no trailing period, match project capitalization after the colon
- Body only if needed: non-obvious why, breaking changes, migration notes, linked issues (`Closes #42`, `Refs #17`); wrap body at 72, issue refs at the end; attribution requests go in a `Co-authored-by:` trailer, never the subject
- **Always include a full body** for breaking changes, security fixes, data migrations, reverts — a subject-only message hides exactly the information future readers need.

**Boundary:** generate the message only — never run `git commit`, stage, or amend. Output a ready-to-paste code block.

**Never:** "This commit does X", "I/we", "now", "currently", AI attribution, emoji (unless convention requires), restating the scope.

---

## 5. Code review

**Rule:** One line per finding — location, problem, fix. No throat-clearing.

**Format:** `L<line>: <problem>. <fix>.` Prefix `<file>:L<line>:` on multi-file diffs. Sort file→line ascending; zero findings → `No issues.`

**Keep:** exact line numbers, exact symbols in backticks, concrete fix, and the why when it's not obvious.

**Severity prefixes:**

- 🔴 bug — broken behavior, will cause an incident
- 🟡 risk — works but fragile (race, missing null check, swallowed error)
- 🔵 nit — style/naming, author can ignore
- ❓ q — genuine question, not a suggestion

**Why:** severity lets the author triage in seconds; hedging ("it seems like...") hides uncertainty — use `q:` instead.

**Complexity-hunt mode** (diff-only): tag findings `delete:` / `stdlib:` / `native:` / `yagni:` / `shrink:`, end with `net: -N lines`. Correctness/security/perf out of scope in this mode; never flag the required smoke test.

**Boundaries:** reviews only — no fixes, no approve/request-changes, no big-refactor proposals, formatting nits skipped unless meaning-changing. Don't guess — if unsure of intent, reference the line and ask (`q:`).

---

## 6. File compression

**Rule:** Compress natural-language files (.md, .txt, .typ, .tex) to cut input tokens. Backups go OUT-OF-TREE (e.g. `%LOCALAPPDATA%` on Windows) so auto-loaders never re-ingest them.

**Remove:** articles, filler, hedging, redundant phrasing ("in order to" → "to"), connective fluff ("however", "furthermore").

**Compress:** short synonyms, fragments OK, drop "you should"/"make sure to"/"remember to", merge redundant bullets, collapse duplicate examples to one.

**Preserve EXACTLY:** code blocks (verbatim), inline backticks, URLs, paths, commands, env vars, technical terms, dates/versions, heading text, bullet nesting, table structure, frontmatter. These carry machine- or link-sensitive meaning.

**Boundaries:**

- NEVER modify .py/.js/.ts/.json/.yaml/.yml/.toml/.env/.lock/.css/.html/.xml/.sql/.sh
- Mixed content → compress prose only; unsure → leave unchanged
- Fail after 2 retries → report error, leave original untouched

---

## 7. Subagent delegation

**Rule:** Use subagents to shrink main context (~60% smaller results). Rule of thumb: want output in 1/3 the tokens → terse subagents; want prose → full-capability agents.

| Task                                            | Use                          |
| ----------------------------------------------- | ---------------------------- |
| "Where is X / what calls Y"                     | investigator                 |
| Same + architecture commentary                  | Explore agent                |
| Surgical edit, ≤2 files, scope obvious          | builder                      |
| New feature / 3+ files / cross-cutting refactor | main thread                  |
| Review diff for bugs                            | reviewer                     |
| Deep review with rationale                      | full-capability review agent |
| One-line answer already known                   | main thread, no subagent     |

**Agent contracts (ultra-terse):**

- **investigator:** locate, report, stop. Never edit, never propose fixes. Rows: `<path:line> — symbol — ≤6-word note`, group headers (Defs:/Refs:/Callers:/Tests:) at 3+ rows. Zero hits → `No match.`
- **builder:** 1 file ideal, 2 OK, 3+ refuse (`too-big.`). Edit existing only; no new abstractions, no drive-by refactors, no comment additions, no Bash. Read → smallest diff → re-read verify → receipt (`verified: OK|mismatch`). Refusal tokens: `needs-confirm.` / `ambiguous.` / `regressed.`
- **reviewer:** findings only, no praise, no scope creep. Security findings: plain-English risk sentence first, then terse fix line.

**Why the contracts matter:** a subagent that expands scope silently burns the context you delegated to save.

**Patterns:** locate→fix→verify chain · parallel scouts (2-3 investigators) · skip the investigator when the site is already known.

**Never:** builder without knowing the file first; investigator→builder chains on 5-file refactors (keep big work in the main thread).

---

## 8. Verification discipline

**Rule:** Translate acceptance conditions into the smallest sufficient proof set. Focused checks before wider gates. Reuse still-current results when the repository state matches. Distinguish pass/fail/unavailable/blocked exactly. Don't edit product code unless the request includes fixes. No polish after criteria pass. **Stop immediately when acceptance proof is complete** — report commands, results, unresolved risk only.

**Why:** "one more small improvement" past the acceptance bar is how verified-good states become unverified states.

---

## Part B — Craft

## 9. JavaScript / TypeScript practices

**Syntax & language**

- `const` by default; `let` only when reassigning; never `var` — block scoping prevents whole classes of bugs
- Strict equality `===`/`!==` — `==` coercion rules are a bug factory
- Prefer destructuring, spread/rest, `?.`, `??`, template literals
- No magic numbers/strings — a named constant documents intent at every use site
- Non-mutating array methods on shared data: `.toSorted()`/`.toReversed()`/`.with()` — in-place mutation breaks other references and React assumptions

**Functions & modules**

- One job per function; pure where possible — side effects pushed to the edges so the core stays testable
- Guard clauses + early returns over deep nesting — flat code reads linearly
- camelCase variables/functions, PascalCase classes/components, SNAKE_CASE constants, verbs for functions
- No circular imports; import from source files rather than barrels when bundle size matters

**Async**

- `async/await` over `.then()` chains; every promise needs a rejection path — a swallowed error is a deferred incident
- Independent awaits run in parallel (`Promise.all`) — sequential awaits over independent work is pure latency
- Timeouts/cancellation for network calls

**Errors & security**

- Fail fast; validate inputs at trust boundaries; typed/thrown errors over sentinel values (a returned `-1` can leak into arithmetic; a throw can't be ignored silently)
- Never `eval`; never build HTML from unsanitized user input (XSS); secrets never in client-reachable code

**Type safety — solid types, never hacky ones**

- TypeScript strict mode when supported
- No `any`, no `as any`, no `@ts-ignore`. Use `unknown` + narrowing when the shape is unclear.
- Never silence the compiler with `as` or `!` just to make an error go away — a type error means the type or the code is wrong; fix the one that's wrong.
- Model real shapes: discriminated unions (`{status: 'loading'|'error'|'success'}`) over boolean-flag soup; exhaustive switches with a `never` check so adding a variant forces every switch to handle it
- Validate untrusted input at boundaries with a schema (e.g. zod) and derive types from it — types then can't drift from runtime reality
- Annotate exported/public signatures precisely; let inference handle locals
- Compose with generics and utility types (`Pick`, `Omit`, `Readonly`, `ReturnType`) instead of copy-pasting near-identical interfaces
- No lying names: a `User` type must match what actually arrives — partial shapes get `Partial<User>`/`Draft` naming

**Tooling & tests**

- ESLint + Prettier enforced in CI, not optional
- Tests follow arrange-act-assert; cover happy path AND failure modes

---

## 10. React & frontend practices

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

---

### 10.2 Types (`src/types/`)

| ID | Rule | Tag |
|----|------|-----|
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
|--------|---------|---------|
| `T_BASE_*` | API response types mirroring backend DTOs | `T_BASE_ENTITY`, `T_BASE_DATA_TABLE` |
| `T_*` | Frontend compositions, mutations, payloads | `T_ENTITY_CREATE_PAYLOAD` |
| `T_*_SLICE` | Zustand store slice types | `T_ENTITIES_SLICE` |

| Suffix | Purpose |
|--------|---------|
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

---

### 10.3 Components

| ID | Rule | Tag |
|----|------|-----|
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
|---|---|---|
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

---

### 10.4 Hooks

| ID | Rule | Tag |
|----|------|-----|
| FE-HOOK-001 | Complex feature logic in `src/hooks/<feature>/use-*.ts` with descriptive names (`expandLineageUpstream`, not `handleExpand`); components stay thin | 👀 |
| FE-HOOK-002 | Hooks that only wrap API calls or streaming go in `src/hooks/api/` | 👀 |
| FE-HOOK-003 | Before writing a generic browser/OS hook (mouse position, screen/viewport size, OS detection, clipboard, online status, etc.), check `@teragonia/uikit`'s hook catalog, then a well-known community hook library — don't hand-roll one that already exists | 👀 |

**FE-HOOK-003 — Reuse before writing:** Check in order: 1) `@teragonia/uikit` hook catalog, 2) Mantine hooks / `usehooks-ts` / `react-use`, 3) Only then write your own.

---

### 10.5 Forms

| ID | Rule | Tag |
|----|------|-----|
| FE-FORM-001 | `@tanstack/react-form` for all form state — never `useState` per field | 👀 |
| FE-FORM-002 | Validate with Zod schemas wired as `validators` — no ad-hoc validation logic | 👀 |
| FE-FORM-003 | Field components in `components/<feature>/form-*.tsx` (e.g. `form-text-field.tsx`); submit through the form's `handleSubmit`, not a separate button `onSubmit` | 👀 |

```typescript
// ✅
const form = useForm({
  defaultValues: { name: "" },
  validators: { onSubmit: myZodSchema },
  onSubmit: async ({ value }) => { await mutate(value) },
})

// ❌
const [name, setName] = useState("")
const handleSubmit = () => {
  if (!name) setError("Required")
  else mutate({ name })
}
```

---

### 10.6 State & Data Fetching

**State ownership (FE-STATE-001):**

| State type | Tool |
|---|---|
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

---

**API Layer (TanStack Query):**

| ID | Rule | Tag |
|----|------|-----|
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

---

### 10.7 Router (TanStack Router)

| ID | Rule | Tag |
|----|------|-----|
| FE-ROUTE-001 | File-based routes in `src/routes/`: `__root.tsx` always-rendered root, `$` prefix dynamic segment captured as `params`, `_` prefix pathless layout, `_` suffix un-nests from parent, `-` prefix excluded from the tree, `(folder)` organizational group | 👀 |
| FE-ROUTE-002 | Commit `routeTree.gen.ts` — it's runtime, not a build artifact | 👀 |
| FE-ROUTE-003 | `autoCodeSplitting: true` in the Vite plugin splits non-critical properties automatically; manual splitting via `.lazy.tsx` + `createLazyFileRoute`; don't split the `loader` without a reason | 👀 |
| FE-ROUTE-004 | Error boundaries at layout or route level | 👀 |

---

### 10.8 Styling & Code Quality

| ID | Rule | Tag |
|----|------|-----|
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

**FE-STYLE-001 — No `styled()`:**
```tsx
// ✅ system props
<Box display="flex" gap={2} p={1} mt={2}>…</Box>

// ✅ sx where it earns it — nested selectors, theme callbacks, pseudo-elements
<Box display="flex" sx={(theme) => ({ "&:hover": { backgroundColor: theme.palette.action.hover } })}>…</Box>

// ❌ styled() wrapper
const StyledContainer = styled(Box)(({ theme }) => ({ display: "flex" }))

// ❌ sx for layout Box already exposes as system props
<Box sx={{ display: "flex", gap: 2 }}>…</Box>
```

**FE-STYLE-007 — Named event handlers:**
```tsx
// ❌
<Button onClick={() => deleteEntity(entity.id)}>Delete</Button>

// ✅
function handleDeleteEntity(event: React.MouseEvent<HTMLButtonElement>) {
  const entityIndex = event.currentTarget.dataset.index
  if (entityIndex) setEntities((prev) => prev.filter((_, i) => i !== Number(entityIndex)))
}
<Button data-index={index} onClick={handleDeleteEntity}>Delete</Button>
```

**FE-STYLE-012 — CSS over JS for presentational transforms:**
```tsx
// ✅
<PrimaryText textTransform="uppercase">{concept.name}</PrimaryText>

// ❌
<PrimaryText>{concept.name.toUpperCase()}</PrimaryText>
```

**FE-STYLE-013 — Rotate via CSS class:**
```tsx
// ✅
<IconRenderer icon={ChevronIcon} className="rotate-90" />
<IconRenderer icon={ChevronIcon} className={isExpanded ? undefined : "rotate--90"} />

// ❌
<IconRenderer icon={ChevronIcon} style={{ transform: "rotate(90deg)" }} />
```

**FE-STYLE-014 — System props over `sx`:**
```tsx
// ❌
<PrimaryText component="span" sx={{ cursor: "help", color: "text.disabled", fontWeight: 500, userSelect: "none", display: "inline-flex" }}>

// ✅
<PrimaryText component="span" color="text.disabled" fontWeight={500} display="inline-flex" sx={{ cursor: "help", userSelect: "none" }}>
```

**FE-STYLE-015 — No stray semicolon in argument list:**
```typescript
// ❌
const ids = items.map(;(item) => item.id)
await run(cb, ;async () => refetch())

// ✅
const ids = items.map((item) => item.id)
await run(cb, async () => refetch())
```

**FE-STYLE-016 — `&&` over ternary-to-null:**
```tsx
// ❌
{showNotifications ? (
  <NotificationsButton anchorPopoverToButton searchParamKey={NOTIFICATIONS_EMBEDDED_SEARCH_PARAM} />
) : null}

// ✅
{showNotifications && (
  <NotificationsButton anchorPopoverToButton searchParamKey={NOTIFICATIONS_EMBEDDED_SEARCH_PARAM} />
)}
```

---

### 10.9 Accessibility & `data-id`

| ID | Rule | Tag |
|----|------|-----|
| FE-ID-001 | Every meaningful DOM element carries a unique kebab-case `data-id`; list rows include the item id; page root is the file basename | 👀 |
| FE-ID-002 | Never `data-testid` / `data-test-id`, and never the `id` attribute for identification — Playwright's `getByTestId` resolves against `data-id` | 🤖 |
| FE-ID-003 | No CSS id/class locators in e2e specs (`.react-flow*` structural classes exempt) | 🤖 |
| FE-A11Y-001 | Every interactive element has an accessible label — `aria-label` or visible text; icon-only buttons always `aria-label`: `<IconButton aria-label="Delete row"><DeleteIcon /></IconButton>` | 👀 |
| FE-A11Y-002 | Dialogs use `aria-labelledby` pointing at the title element | 👀 |
| FE-A11Y-003 | Keyboard navigation is verified in the browser before a feature is done — Tab order, focus-reachable affordances, Escape, shortcut conflicts | 👀 |
| FE-A11Y-004 | Never `tabIndex` > 0 | 👀 |
| FE-A11Y-005 | Semantic HTML via MUI's `component` prop (`component="nav"`, `"main"`, `"section"`) | 👀 |
| FE-A11Y-006 | Non-`<button>` click targets also handle `onKeyDown` for Enter/Space — or become a `<button>` | 👀 |

**FE-ID-001 example:**
```tsx
<Box data-id="data-sources-page">                       {/* page root */}
  <Button data-id="data-sources-add-button">Add</Button>
  {rows.map((row) => <TableRow key={row.id} data-id={`data-sources-row-${row.id}`} />)}
</Box>
```

**FE-A11Y-003 — Keyboard verification checklist:**
- Tab order matches visual order (check what sits between expected tab stops)
- Every mouse affordance has a keyboard equivalent (pair `:hover` with `:focus-within` / `:focus-visible`)
- Escape does something sensible where it applies (progressive Escape: first clears, second blurs/closes)
- New global shortcuts don't collide (search existing keydown listeners, make shortcut inert when target off-screen)

---

### 10.10 React Flow (`@xyflow/react` v12)

| Term | Definition |
|---|---|
| **Nodes** | Elements with position and content; customizable via `nodeTypes` |
| **Handles** | Connection points; `type="source"` or `type="target"` |
| **Edges** | SVG paths; require `source`, `target` |

Built-in edge types: `default` (bezier), `smoothstep`, `step`, `straight`. Reference pattern: `frontend/src/components/data-model-lineage/data-model-lineage.tsx`.

| ID | Rule | Tag |
|----|------|-----|
| FE-RF-001 | Custom nodes registered via `nodeTypes`, edges via `edgeTypes`; custom edges built with `BaseEdge` plus a path helper (`getBezierPath`, `getSmoothStepPath`, …) | 👀 |
| FE-RF-002 | Each handle needs a unique `id` and edges reference it via `sourceHandle` / `targetHandle`; hide with `visibility: hidden` (not `display: none`); call `useUpdateNodeInternals` when handle count or position changes | 👀 |
| FE-RF-003 | Filter `type === "select"` out of `onNodesChange` / `onEdgesChange` before calling `applyNodeChanges` / `applyEdgeChanges` | 👀 |
| FE-RF-004 | Never call a Zustand setter unconditionally in `onSelectionChange` — compare against `getState()` first | 👀 |
| FE-RF-005 | Before marking any React Flow/Zustand change done: `pnpm tsc -b` **and** load the page in a browser | 🧠 |

**FE-RF-003 — Filter select changes:**
```tsx
function onNodesChange(changes: NodeChange<T>[]) {
  const nonSelect = changes.filter((c) => c.type !== "select")
  if (nonSelect.length === 0) return
  setNodes((snap) => applyNodeChanges(nonSelect, snap))
}
```

**FE-RF-004 — Guard `onSelectionChange`:**
```tsx
const prev = useGlobalStore.getState().graphViewSelectedConceptIds[dataModelId] ?? []
const changed = next.length !== prev.length || next.some((id, i) => id !== prev[i])
if (changed) setGraphViewSelectedConceptIds(dataModelId, next)
```

**FE-RF-005 — Build gate:** Runtime render loops and stale HMR state are invisible to type-checking. Note: `pnpm tsc --noEmit` from repo root checks **zero files** (solution-style tsconfig); use `pnpm tsc -b` or `pnpm build`.

---

### 10.11 Architecture & UX (Project-Specific)

| ID | Rule | Tag |
|----|------|-----|
| FE-ENG-001 | No secrets in the bundle; `VITE_*` prefix only for values that may reach the browser | 👀 |
| FE-ENG-002 | `target="_blank"` always with `rel="noopener noreferrer"` | 👀 |
| FE-ENG-003 | Virtualize high-row-count lists; debounce expensive handlers | 🧠 |
| FE-ENG-004 | Route-level code splitting and splitting for heavy widgets | 👀 |
| FE-ENG-005 | Small focused PRs with conventional commit messages | 👀 |

---

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

---

### 10.13 React 19 New Hooks

React 19 introduces several new hooks that simplify common patterns. Use these instead of hand-rolled solutions.

| Hook | Purpose | When to Use |
|------|---------|-------------|
| `useOptimistic` | Optimistic UI updates during async operations | Form submissions, mutations where immediate feedback improves UX |
| `useActionState` | Manage form action state (pending, data, error) | TanStack Form submissions, server actions |
| `useFormStatus` | Read parent `<form>` submission status in child components | Disable submit buttons, show pending UI in nested components |
| `use()` | Read promises/context during render | Replace `useEffect` + state for data fetching in Server Components |
| `useDeferredValue` (initial value) | Defer non-urgent updates with initial value | Search inputs, filtering large lists |

**`useOptimistic` + TanStack Form pattern:**
```tsx
import { useOptimistic, useActionState } from 'react';
import { useForm } from '@tanstack/react-form';

function CommentForm({ initialComments }) {
  const [optimisticComments, addOptimisticComment] = useOptimistic(
    initialComments,
    (current, newComment) => [...current, newComment]
  );

  const [formState, formAction] = useActionState(
    async (prev, formData) => {
      const response = await fetch('/api/comments', {
        method: 'POST',
        body: formData,
      });
      return response.json();
    },
    null
  );

  const form = useForm({
    defaultValues: { text: '' },
    onSubmit: async ({ value }) => {
      const optimisticComment = { id: `temp-${Date.now()}`, text: value.text, pending: true };
      addOptimisticComment(optimisticComment);
      formAction(new FormData().append('text', value.text));
    },
  });

  return (
    <form onSubmit={form.handleSubmit}>
      <ul>
        {optimisticComments.map(c => <li key={c.id}>{c.text}{c.pending && ' (sending...)'}</li>)}
      </ul>
      <input {...form.getFieldProps('text')} />
      <button type="submit" disabled={formState?.pending}>Send</button>
    </form>
  );
}
```

**`useFormStatus` for nested submit button:**
```tsx
import { useFormStatus } from 'react-dom';

function SubmitButton() {
  const { pending } = useFormStatus();
  return <button type="submit" disabled={pending}>{pending ? 'Submitting...' : 'Submit'}</button>;
}

// Usage in parent form
<form action={formAction}>
  <SubmitButton />
</form>
```

**`useActionState` with TanStack Form:**
```tsx
import { useActionState } from 'react';
import { useForm } from '@tanstack/react-form';

function LoginForm() {
  const [error, submitAction, isPending] = useActionState(
    async (prev, formData: FormData) => {
      const res = await fetch('/api/login', { method: 'POST', body: formData });
      if (!res.ok) return 'Invalid credentials';
      return null;
    },
    null
  );

  const form = useForm({
    defaultValues: { email: '', password: '' },
    onSubmit: ({ value }) => submitAction(new URLSearchParams(value)),
  });

  return (
    <form onSubmit={form.handleSubmit}>
      {error && <div role="alert">{error}</div>}
      <input {...form.getFieldProps('email')} />
      <input {...form.getFieldProps('password')} type="password" />
      <button type="submit" disabled={isPending}>{isPending ? 'Logging in...' : 'Login'}</button>
    </form>
  );
}
```

---

### 10.14 Mantine Hooks (Copy-Paste Only)

**Rule:** Do not install `@mantine/hooks`. Copy needed hooks from Mantine source into `src/hooks/mantine/` — they are MIT-licensed, dependency-free, and solve SSR/cleanup edge cases correctly.

**Approved hooks to copy (reference: https://github.com/mantinedev/mantine/tree/master/packages/@mantine/hooks/src):**

| Hook | Use Case | Source File |
|------|----------|-------------|
| `useDebouncedState` | Search inputs, auto-save | `use-debounced-state/use-debounced-state.ts` |
| `useDebouncedValue` | Debounce values with cancel/flush | `use-debounced-value/use-debounced-value.ts` |
| `useLocalStorage` | Persist state to localStorage (SSR-safe) | `use-local-storage/use-local-storage.ts` |
| `useSessionStorage` | Persist to sessionStorage | `use-session-storage/use-session-storage.ts` |
| `useMediaQuery` | Responsive breakpoints | `use-media-query/use-media-query.ts` |
| `useHotkeys` | Global keyboard shortcuts | `use-hotkeys/use-hotkeys.ts` |
| `useClickOutside` | Dropdown/modal close on outside click | `use-click-outside/use-click-outside.ts` |
| `useViewportSize` | Window dimensions | `use-viewport-size/use-viewport-size.ts` |
| `useReducedMotion` | Respect `prefers-reduced-motion` | `use-reduced-motion/use-reduced-motion.ts` |
| `useIntersection` | Lazy load, infinite scroll | `use-intersection/use-intersection.ts` |
| `useScrollIntoView` | Smooth scroll to element | `use-scroll-into-view/use-scroll-into-view.ts` |
| `useClipboard` | Copy to clipboard | `use-clipboard/use-clipboard.ts` |
| `useHash` | URL hash state | `use-hash/use-hash.ts` |
| `useIdle` | User idle detection | `use-idle/use-idle.ts` |
| `useWindowEvent` | Typed window event listeners | `use-window-event/use-window-event.ts` |

**Copy pattern — create `src/hooks/mantine/use-debounced-state.ts`:**
```typescript
import { useCallback, useEffect, useRef, useState } from 'react';

export interface UseDebouncedStateOptions {
  leading?: boolean;
}

export type UseDebouncedStateReturnValue<T> = [T, (newValue: T | ((prev: T) => T)) => void];

export function useDebouncedState<T = any>(
  defaultValue: T,
  wait: number,
  options?: UseDebouncedStateOptions
): UseDebouncedStateReturnValue<T> {
  const [value, setValue] = useState(defaultValue);
  const timeoutRef = useRef<ReturnType<typeof setTimeout>>();
  const cooldownRef = useRef(false);
  const mountedRef = useRef(false);

  const cancel = useCallback(() => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
      timeoutRef.current = undefined;
    }
    cooldownRef.current = false;
  }, []);

  const flush = useCallback(() => {
    if (timeoutRef.current) {
      cancel();
      cooldownRef.current = false;
      setValue(value);
    }
  }, [cancel, value]);

  useEffect(() => {
    if (mountedRef.current) {
      if (!cooldownRef.current && options?.leading) {
        cooldownRef.current = true;
        setValue(value);
        timeoutRef.current = setTimeout(() => {
          cooldownRef.current = false;
        }, wait);
      } else {
        cancel();
        timeoutRef.current = setTimeout(() => {
          cooldownRef.current = false;
          setValue(value);
        }, wait);
      }
    }
  }, [value, options?.leading, wait, cancel]);

  useEffect(() => {
    mountedRef.current = true;
    return cancel;
  }, [cancel]);

  return [value, setValue];
}
```

**TypeScript types:** Copy `UseDebouncedStateOptions`, `UseDebouncedStateReturnValue` from source. Export them for consumers.

**Why copy-paste:** Zero dependencies, tree-shakable, no version drift, full TypeScript support, customizable.

---

### 10.15 Error Boundaries (Client-Only)

**Rule:** Use `react-error-boundary` v6+ (TypeScript-first). No class components.

**Install:** `pnpm add react-error-boundary`

**Root error boundary (wraps routes):**
```tsx
// src/components/error-boundary/root-error-boundary.tsx
'use client';
import { ErrorBoundary, FallbackProps } from 'react-error-boundary';
import { Alert, Button } from '@mui/material';

function RootFallback({ error, resetErrorBoundary }: FallbackProps) {
  return (
    <Alert severity="error" sx={{ mb: 2 }}>
      Something went wrong. <pre>{error.message}</pre>
      <Button onClick={resetErrorBoundary} variant="outlined" sx={{ mt: 1 }}>
        Try again
      </Button>
    </Alert>
  );
}

export function RootErrorBoundary({ children }: { children: React.ReactNode }) {
  return (
    <ErrorBoundary
      fallbackRender={RootFallback}
      onError={(error) => console.error('Root error:', error)}
      onReset={() => console.log('Error boundary reset')}
    >
      {children}
    </ErrorBoundary>
  );
}
```

**Route-scoped boundary with `resetKeys`:**
```tsx
// src/routes/__root.tsx
import { RootErrorBoundary } from '@/components/error-boundary/root-error-boundary';
import { Outlet } from '@tanstack/react-router';

export function Route() {
  return (
    <RootErrorBoundary>
      <Outlet />
    </RootErrorBoundary>
  );
}

// Feature-specific boundary (resets on entityId change)
<ErrorBoundary
  fallbackRender={({ error, resetErrorBoundary }) => (
    <Alert severity="error">Failed to load entity. <Button onClick={resetErrorBoundary}>Retry</Button></Alert>
  )}
  resetKeys={[entityId]}
  onError={(e) => console.error('Entity error:', e)}
>
  <EntityDetail entityId={entityId} />
</ErrorBoundary>
```

**Async errors in event handlers (React 19 `useTransition`):**
```tsx
import { useTransition } from 'react';
import { ErrorBoundary, useErrorBoundary } from 'react-error-boundary';

function SaveButton() {
  const [isPending, startTransition] = useTransition();
  const { showBoundary } = useErrorBoundary();

  const handleSave = async () => {
    startTransition(async () => {
      try {
        await saveData();
      } catch (e) {
        showBoundary(e);
      }
    });
  };

  return <button onClick={handleSave} disabled={isPending}>Save</button>;
}
```

**Error boundary props reference:**

| Prop | Type | Purpose |
|------|------|---------|
| `fallbackRender` | `({ error, resetErrorBoundary }) => ReactNode` | Dynamic fallback with retry |
| `resetKeys` | `unknown[]` | Reset boundary when keys change (route params) |
| `onError` | `(error, info) => void` | Log to console/error service |
| `onReset` | `() => void` | Cleanup state on retry |

---

### 10.16 Security & XSS Prevention

**Rule:** Never render unsanitized user content. All dynamic HTML goes through DOMPurify.

**Install:** `pnpm add dompurify @types/dompurify`

**Sanitized HTML rendering:**
```tsx
import DOMPurify from 'dompurify';

function SafeHtml({ html }: { html: string }) {
  const sanitized = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'u', 'a', 'ul', 'ol', 'li'],
    ALLOWED_ATTR: ['href', 'target', 'rel'],
  });
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

**URL validation (prevent `javascript:` injection):**
```tsx
export function validateUrl(url: string): string {
  try {
    const parsed = new URL(url);
    if (!['http:', 'https:'].includes(parsed.protocol)) return '';
    return parsed.toString();
  } catch {
    return '';
  }
}

// Usage
<a href={validateUrl(userUrl) || '#'}>Link</a>
```

**CSP for static hosting (Vite build):**
```html
<!-- index.html -->
<meta http-equiv="Content-Security-Policy"
  content="default-src 'self';
           style-src 'self' 'unsafe-inline';
           script-src 'self' 'unsafe-inline';
           img-src 'self' data: blob:;
           font-src 'self' data:;
           connect-src 'self' https://api.yourdomain.com;" />
```

**MUI v6 CSP requirements (static hosting):**
- `style-src 'self' 'unsafe-inline'` — Emotion injects inline styles
- `script-src 'self' 'unsafe-inline'` — Required for inline scripts
- Add `connect-src` for your API domain

**Token storage:** HttpOnly cookies set by server. Client never accesses tokens directly. Use `credentials: 'include'` on fetch.

**Never do:**
- `element.innerHTML = userInput`
- `dangerouslySetInnerHTML={{ __html: userInput }}` without DOMPurify
- `eval()` or `new Function(userInput)`
- Store tokens in `localStorage`/`sessionStorage`

---

### 10.17 Testing: Vitest + Playwright

**Stack:** Vitest (unit/component) + Playwright (e2e). **No mock data** — use real API via MSW in tests.

**Install:**
```bash
pnpm add -D vitest @vitest/browser-react playwright @playwright/test msw
```

**Vitest config (`vitest.config.ts`):**
```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    browser: {
      enabled: true,
      provider: 'playwright',
      instances: [{ browser: 'chromium' }],
    },
    setupFiles: ['./vitest.setup.ts'],
    coverage: { thresholds: { lines: 80, functions: 80, branches: 70, statements: 80 } },
  },
});
```

**Test utilities (`vitest.setup.ts`):**
```typescript
import { cleanup } from '@testing-library/react';
import { afterEach, vi } from 'vitest';
import '@testing-library/jest-dom';
import { setupServer } from 'msw/node';
import { handlers } from './__mocks__/handlers';

export const server = setupServer(...handlers);

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));
afterEach(() => { cleanup(); server.resetHandlers(); });
afterAll(() => server.close());

// Mock window.matchMedia for MUI
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: vi.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: vi.fn(),
    removeListener: vi.fn(),
  })),
});
```

**Component test (`src/components/button/button.test.tsx`):**
```tsx
import { render } from '@vitest/browser-react';
import { expect, test } from 'vitest';
import { Button } from './button';

test('button clicks increment counter', async () => {
  const screen = await render(<Button>Click me</Button>);
  await screen.getByRole('button').click();
  await expect.element(screen.getByText('Clicked 1 times')).toBeVisible();
});
```

**Hook test (`src/hooks/use-counter/use-counter.test.ts`):**
```tsx
import { renderHook } from '@vitest/browser-react';
import { expect, test } from 'vitest';
import { useCounter } from './use-counter';

test('counter increments', async () => {
  const { result, act } = await renderHook(() => useCounter());
  await act(() => result.current.increment());
  expect(result.current.count).toBe(1);
});
```

**Playwright e2e (`tests/e2e/login.spec.ts`):**
```typescript
import { test, expect } from '@playwright/test';

test('user can login', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[data-id="login-email"]', 'user@example.com');
  await page.fill('[data-id="login-password"]', 'password');
  await page.click('[data-id="login-submit"]');
  await expect(page.locator('[data-id="dashboard"]')).toBeVisible();
});
```

**MSW handlers (`__mocks__/handlers.ts`):**
```typescript
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/user', () => HttpResponse.json({ name: 'Test User' })),
  http.post('/api/login', async ({ request }) => {
    const body = await request.json();
    if (body.email === 'user@example.com') return HttpResponse.json({ token: 'abc' });
    return HttpResponse.json({ error: 'Invalid' }, { status: 401 });
  }),
];
```

**Rules:**
- No `vi.mock()` for internal modules — test real implementations
- Use `data-id` selectors only (FE-ID-001)
- Component tests: render with providers (QueryClient, Zustand store)
- Coverage thresholds enforced in CI

---

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

---

### 10.19 TanStack Query v5 + Forms

**Query factory pattern (FE-API-003):**
```typescript
// src/api/queries/use-query-entities.ts
import { generateQueryOptions } from '@/api/helpers/generate-query-options';
import { ENTITY_ENDPOINTS } from '@/constants/api-endpoints';

export function entitiesQueryOptions() {
  return generateQueryOptions<T_BASE_ENTITY[]>({
    queryKey: ['entities'],
    url: ENTITY_ENDPOINTS.LIST(),
    staleTime: 5000,
    select: (data) => data.map(transformEntity), // transform T_BASE_* → T_*
  });
}

export function useQueryEntities() {
  return useQuery(entitiesQueryOptions());
}
```

**Optimistic updates (cache-based, FE-API-009):**
```typescript
// src/api/mutations/use-mutate-create-entity.ts
import { useCustomMutation } from '@/api/helpers/use-custom-mutation';
import { ENTITY_ENDPOINTS } from '@/constants/api-endpoints';
import { entitiesQueryOptions } from '@/api/queries/use-query-entities';

export function useCreateEntity() {
  return useCustomMutation({
    url: ENTITY_ENDPOINTS.CREATE(),
    successMessage: 'Entity created',
    errorMessage: 'Failed to create entity',
    invalidateQueryKeys: [entitiesQueryOptions().queryKey],
    onMutate: async (newEntity) => {
      await queryClient.cancelQueries({ queryKey: entitiesQueryOptions().queryKey });
      const previous = queryClient.getQueryData(entitiesQueryOptions().queryKey);
      queryClient.setQueryData(entitiesQueryOptions().queryKey, (old) => [
        ...(old ?? []),
        { ...newEntity, id: `temp-${Date.now()}`, pending: true },
      ]);
      return { previous };
    },
    onError: (_err, _vars, context) => {
      queryClient.setQueryData(entitiesQueryOptions().queryKey, context?.previous);
    },
  });
}
```

**Cross-component pending UI (`useMutationState`):**
```tsx
import { useMutationState } from '@tanstack/react-query';

function EntityList() {
  const creatingMutations = useMutationState({
    filters: { mutationKey: ['createEntity'], status: 'pending' },
    select: (m) => m.state.variables,
  });

  return (
    <>
      {creatingMutations.map((vars, i) => (
        <Skeleton key={i} data-id="entity-creating-skeleton" />
      ))}
      <EntityTable />
    </>
  );
}
```

**TanStack Form + Zod validation (FE-FORM-001/002):**
```tsx
import { useForm } from '@tanstack/react-form';
import { zodValidator } from '@tanstack/zod-form-adapter';
import { z } from 'zod';

const schema = z.object({
  name: z.string().min(1, 'Required'),
  email: z.string().email('Invalid email'),
});

function EntityForm() {
  const form = useForm({
    defaultValues: { name: '', email: '' },
    validators: { onSubmit: zodValidator(schema) },
    onSubmit: async ({ value }) => {
      await useCreateEntity().mutateAsync(value);
    },
  });

  return (
    <form onSubmit={form.handleSubmit}>
      <input {...form.getFieldProps('name')} />
      {form.getFieldState('name').meta.errors[0] && <span>{form.getFieldState('name').meta.errors[0]}</span>}
      <input {...form.getFieldProps('email')} />
      <button type="submit">Save</button>
    </form>
  );
}
```

---

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

---

## 11. Test-Driven Development

**Red → Green → Refactor** — but refactor happens at _code-review_, not inside the loop. Keeps the loop fast; review catches smells separately.

- **Write the failing test first** — prevents implementing imagined behavior
- **One vertical slice per cycle** (one seam, one test, minimal impl) — each cycle teaches the next
- **Test only at pre-agreed seams** (public interfaces) — tests survive refactors; tests coupled to internals break on every internal change
- **No horizontal slicing** (all tests first, then impl) — leads to tautological tests for imagined APIs
- **Expected values from an independent source** (literals, spec, worked example) — `expect(add(a,b)).toBe(a+b)` passes by construction and proves nothing

**Anti-patterns to kill:** implementation-coupled tests (mocking internals, testing private methods), tautological tests, testing details instead of behavior.

---

## 12. Implementation workflow

**Flow:** Grill → Spec → Tickets (vertical slices) → Implement (TDD) → Code-Review → Commit

- **Vertical slices:** each ticket cuts through schema, API, UI, tests — demo-able end-to-end, avoids layer-by-layer integration hell
- **Blockers first:** declare blocking edges, work the frontier — enables parallel work, CI stays green
- **Prefer existing seams;** new seams only at the highest point — fewer seams = less surface = simpler tests
- **Spec template:** Problem → Solution → User Stories → Impl Decisions → Testing Decisions → Out of Scope. Complete context survives context loss.
- **Ticket template:** what to build (user perspective) + Blocked by + Acceptance criteria — self-contained and verifiable
- **Wide refactors = expand → contract:** add new form, migrate in batches, delete old — never breaks CI, blast radius contained
- **Run typechecking often, single tests often, full suite once at end** — fast feedback catches regressions early

---

## 13. Code review — two axes

Review **Standards** and **Spec** separately — never merge them. A change can be clean code that builds the wrong thing.

**Standards axis (Fowler smell baseline):**

| Smell                  | Signal                                      | Fix                                    |
| ---------------------- | ------------------------------------------- | -------------------------------------- |
| Mysterious Name        | Name doesn't reveal purpose                 | Rename; if impossible, design is murky |
| Duplicated Code        | Same logic in multiple hunks                | Extract shared shape                   |
| Feature Envy           | Method reaches into another's data          | Move method to the data                |
| Data Clumps            | Same fields travel together                 | Bundle into a type                     |
| Primitive Obsession    | Primitive stands for a domain concept       | Give the concept its own type          |
| Repeated Switches      | Same switch on same type recurs             | Polymorphism or shared map             |
| Shotgun Surgery        | One change → edits across many files        | Gather into one module                 |
| Speculative Generality | Abstraction for needs the spec doesn't have | Delete; inline back                    |
| Middle Man             | Class just delegates                        | Cut it; call target direct             |

**Rule:** documented repo standard **always overrides** baseline smell.

**Spec axis:** requirements missing/partial · scope creep (behavior not asked for) · implemented but wrong (quote the spec line).

**Output:** two separate reports under `## Standards` and `## Spec`.

---

## 14. Deep module design

**Vocabulary (use exactly):**

- **Module** — anything with interface + implementation (function, class, package)
- **Interface** — everything the caller must know (types, invariants, errors, perf)
- **Depth** — leverage at the interface (lots of behavior, small interface)
- **Seam** — location where an interface lives
- **Adapter** — concrete thing satisfying the interface at a seam
- **Implementation** — what's inside (distinct from Adapter, the role at a seam)
- **Leverage** — capability per unit of interface learned
- **Locality** — change/bugs/knowledge concentrated in one place

**Principles:**

- Depth is an interface property, not an implementation property — internal seams are fine
- **Deletion test:** delete a module — if complexity vanishes, it was pass-through; if it fans out to N callers, it earned its keep
- **Interface = test surface:** if you want to test _past_ the interface, the module shape is wrong
- **One adapter = hypothetical seam; two = real.** Don't introduce a seam unless something actually varies.

**Testable interface patterns:**

```typescript
// Good: accept dependencies
function processOrder(order, paymentGateway) {}

// Bad: create dependencies inside
function processOrder(order) {
  const gateway = new StripeGateway();
}

// Good: return results
function calculateDiscount(cart): Discount {}

// Bad: side effects
function applyDiscount(cart): void {
  cart.total -= discount;
}
```

---

## 15. Debugging discipline

**Phase 1 — build a tight feedback loop (90% of the fix).** Pick the loop type: failing test at seam · curl/HTTP script · CLI + fixture diff · headless browser · replay captured trace · throwaway harness · property/fuzz loop ("sometimes wrong") · bisection harness · differential loop (old vs new) · HITL script. Tighten: faster, sharper signal (assert the exact symptom), deterministic (pin time, seed RNG).

**Why:** debugging speed is feedback-loop speed; everything after is mechanical.

**Phase 2:** Reproduce → minimize to smallest red scenario (every element load-bearing).

**Phase 3:** 3-5 **ranked, falsifiable hypotheses** before testing. Format: "If X causes it, changing Y makes it disappear." Guessing without hypotheses = random walk.

**Phase 4:** Instrument one variable at a time. Debugger > targeted logs > never "log everything." Tag debug logs `[DEBUG-xxxx]` for cleanup.

**Phase 5:** Regression test **before** the fix, at the correct seam. If no correct seam exists, that's the finding — the architecture prevents lockdown.

**Phase 6:** Cleanup — original repro green, regression test passes, debug logs removed, correct hypothesis named in the commit message.

---

## 16. Domain modeling & ADRs

- Challenge terms against `CONTEXT.md` immediately — prevents vocabulary drift
- Sharpen fuzzy/overloaded terms ("account" = Customer or User?) — precision prevents bugs
- Stress-test with concrete edge-case scenarios — forces boundary clarity
- Cross-reference with code: "you said X, code does Y" — catches implementation drift
- Update `CONTEXT.md` inline when a term resolves — decisions captured while fresh
- `CONTEXT.md` = glossary only, no implementation details — stays stable, doesn't rot
- ADR only when: hard to reverse + surprising without context + real trade-off. Anything less is ADR spam.

---

## 17. Grilling decisions (stress-test before building)

- Map the decision as a design tree: decisions branch into dependent decisions — makes dependencies explicit
- Work in **rounds**: each round asks the whole **frontier** (all questions whose prerequisites are settled) — prevents premature answers, enables parallel discovery
- Number each question and give a recommended answer — forces clear options
- **Facts = agent's job** — dispatch subagents to investigate, don't ask the user
- **Decisions = user's job** — put each decision to them and wait. Ownership stays with the user.
- Done when the frontier is empty (no silent assumptions) — prevents "I thought you meant..." later

## 18. Architecture improvement (deepening scan)

Scan for shallow modules and deepen them:

| Friction signal                                               | Deepening opportunity                      |
| ------------------------------------------------------------- | ------------------------------------------ |
| Understanding requires bouncing between many small modules    | Merge into a deeper module                 |
| Interface nearly as complex as implementation                 | Hide complexity behind a smaller interface |
| Pure functions extracted for testability, bugs in call chains | Restore locality                           |
| Tightly-coupled modules leak across seams                     | Redraw the seam; add an adapter            |
| Untested or hard to test through current interface            | Redesign the interface for testability     |

Apply the **deletion test** (section 14) to suspected shallow modules.

## 19. Research, triage, prototyping

**Research:** delegate to a background agent; primary sources only (official docs, source, specs) — secondary sources distort. Follow every claim back to the owning source; output a single Markdown file with citations. Benchmark/savings numbers come from real runs only — never invent per-repo figures.

**Triage:** verify the claim first (reproduce the bug, confirm the diff works). Redundancy check by domain concept, not wording. States: `needs-triage` · `needs-info` · `ready-for-agent` (fully specified, agent can implement AFK) · `ready-for-human` · `wontfix`.

**Prototyping:** a prototype is throwaway code that answers ONE question. Pick the branch — Logic (state-machine feel) or UI (appearance); wrong branch = wasted prototype. Mark clearly as throwaway; trivial to run (one command); no persistence, no tests, no polish — speed of learning beats code quality. Surface state after every action so the user's mental model gets validated. Capture the decision afterward: fold into real code, commit to a throwaway branch, or link from the issue.

---

## 20. Agent skills (SKILL.md) — authoring

**Anatomy:** skill = directory: `SKILL.md` (required, exact spelling) + optional `scripts/`, `references/`, `assets/`; no README.md inside the folder. Frontmatter: `name` kebab-case ≤64 chars matching the folder name; `description` <1024 chars, third person.

**Description discipline:** must answer BOTH _what it does_ AND _when to use_ — include trigger phrases the user would actually say, key terms, file types. Vague descriptions ("helps with documents") never fire. Add anti-triggers (when NOT to use) when confusion is likely.

**Progressive disclosure — three tiers:** metadata always in context (~100 tokens/skill) → SKILL.md body loaded on trigger (<5k tokens) → `references/` files read only when needed. Cheap to carry, deep on demand.

**Body discipline:**

- Under 500 lines; target 150-300. Past ~500 you almost always have 2-3 skills masquerading as one — split by lifecycle / role / level.
- Highest-signal first: one-line bolded summary → When to Use (3-6 concrete situations) → core concept (≤5 sentences) → minimal example → deeper patterns → anti-patterns
- Imperative instructions ("Do X"), never "consider X"; runnable examples with zero boilerplate the reader already knows
- Exclude: feature history, marketing prose, exhaustive edge cases (pick top 3), things the model already knows, long API-param tables (link official docs)
- Scannability test: a 10-second skim answers what is this / when do I use it / minimal example

**Scripts:** deterministic script > model generation wherever possible — code for reliability, instructions for judgment, references for factual lookup. Handle errors explicitly inside scripts — never "fail and let the model figure it out". No voodoo constants (justify every value); state dependencies explicitly and verify availability. MCP tools fully qualified (`ServerName:tool_name`) — bare names fail with multiple servers. Complex multi-step ops: numbered steps + copyable checklist; validation loop (draft → check → fix → repeat until pass).

**Lifecycle:** build the skill AFTER solving the task once manually — extract the winning approach. Trigger tests: fires on obvious tasks ✓, fires on paraphrases ✓, does NOT fire on unrelated topics ✗; ≥3 evals from REAL past tasks; retest across models. Version skills like code and re-test whenever referenced data/schema changes — a skill can pass every check and still be confidently wrong on stale data.

**Security:** frontmatter is injected into the system prompt — treat third-party skills as untrusted input (~26% of community skills carry security issues); review before install.

## 21. Global overrides (apply to everything above)

**Auto-Clarity — drop terse/lazy mode for:**

- Security warnings
- Irreversible action confirmations
- Multi-step sequences where fragment order risks misread
- Compression creating technical ambiguity
- User asks to clarify or repeats a question
- CVE-class findings and architectural disagreements → full explanation paragraphs

Resume terse after the clear part is done. Warnings written in the session's language.

**Language:** reply in the language the user writes — never switch; compress the style, not the language. Keep technical terms, code, API names, CLI commands, commit-type keywords, and exact error strings verbatim unless translation is requested. In particle languages (where markers carry case/role), keep the markers — compress politeness/filler instead.

**Boundaries:** artifacts persisted outside chat stay in normal prose — code, comments, commits, docs, issue/PR text, memory files, third-party messages (compressed files exempt). Terse is for conversation, not for things that outlive the session.

**Deactivation:** terse mode ends only by explicit user command ("stop" / "normal mode").

---

## Quick Reference Card

```
Comm:          Terse, drop filler, never drop negations; full prose outside chat
Minimalism:    YAGNI → reuse → stdlib → native → installed dep → one line → write it
Commit:        type(scope): imperative summary; why over what; body for breaking changes
Review:        L<line>: problem. fix. — separate Standards and Spec axes
TDD:           Red → Green (one slice) → Review (refactor); test at seams only
Implement:     Grill → Spec → Tickets (vertical, blocked) → TDD → Review → Commit
Design:        Deep modules (small interface, lots hidden) at clean seams; deletion test
Debug:         Tight loop → Minimise → 3-5 hypotheses → Instrument one var → Regression test
Domain:        CONTEXT.md glossary + ADRs for hard/reversible/surprising decisions
Grill:         Design tree → frontier rounds → agent finds facts, user decides
Arch:          Scan friction signals → deletion test → deepen shallow modules
Skills:        Solve once manually → extract; what+when in description; test triggers
Verify:        Smallest sufficient proof, then STOP
```
