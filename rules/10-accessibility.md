### 10.9 Accessibility & `data-id`

| ID | Rule | Tag |
| ---- | ------ | ----- |
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
```