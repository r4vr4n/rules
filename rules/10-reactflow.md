### 10.10 React Flow (`@xyflow/react` v12)

| Term | Definition |
| --- | --- |
| **Nodes** | Elements with position and content; customizable via `nodeTypes` |
| **Handles** | Connection points; `type="source"` or `type="target"` |
| **Edges** | SVG paths; require `source`, `target` |

Built-in edge types: `default` (bezier), `smoothstep`, `step`, `straight`. Reference pattern: `frontend/src/components/data-model-lineage/data-model-lineage.tsx`.

| ID | Rule | Tag |
| ---- | ------ | ----- |
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