# 10. Keyboard Navigation & Shortcuts Best Practices

**Applicable AGENTS.md Sections:** FE-A11Y-001 through FE-A11Y-006, FE-RF-003, FE-RF-004

## Keyboard Navigation

### Tab Order
- **Verify visual tab order matches logical navigation order** — Check what sits between expected tab stops.
- **Never use `tabIndex > 0`** (FE-A11Y-004) — If an element needs to be focusable, it should be naturally focusable (`<button>`, `<link>`, `<input>`) or use `role="dialog"` with proper alertdialog patterns.
- **Skip links** — Provide a "Skip to main content" link as the first focusable element for keyboard users.

### Focus-Reachable Affordances
- **Pair `:hover` with `:focus-within` / `:focus-visible`** — Every mouse affordance must have a keyboard equivalent.
- **Escape key** — Progressive Escape: first clears, second blurs/closes. Never leave focus in a state where Escape has no effect.
- **Focus management in modals/dialogs** — Focus should be trapped inside the modal on open and return to the previous focus element on close.
- **Landmark regions** — Use semantic HTML (`<nav>`, `<main>`, `<section>`, `<header>`, `<footer>`) with MUI's `component` prop for screen reader navigation.

### Keyboard Shortcuts
- **Avoid `tabIndex > 0`** — Per FE-A11Y-004, never use `tabIndex` greater than 0.
- **Explicit shortcuts** — Document keyboard shortcuts clearly. Use `aria-label` on icon-only buttons (`<IconButton aria-label="Delete row">`).
- **No conflicting shortcuts** — New global shortcuts must not collide with existing ones. Search existing keydown listeners and make shortcuts inert when target is off-screen.
- **Visual focus indicator** — Ensure `outline` or `:focus-visible` styles are present so keyboard users can see where focus is.
- **Modifier key combinations** — When using `Cmd/Ctrl + X` shortcuts, ensure they don't conflict with browser native shortcuts. Provide a way to disable or customize them.

### Forms & Inputs
- **Accessible labels** — Every interactive element must have an accessible label via `aria-label` or visible text (FE-A11Y-001).
- **Form field association** — Use `<label>` elements connected to inputs via `for`/`id`, or `aria-labelledby` for composite widgets.
- **Error identification** — Programmatically associate error messages with fields using `aria-describedby` or `form.errorMessage` from TanStack Form.
- **Disabled state** — Use native `disabled` attribute on form elements rather than `tabIndex=-1` or CSS-only disabling.

### React Flow Specific
- **Handle visibility** — Hide handles with `visibility: hidden` (not `display: none`) per FE-RF-002.
- **Selection change guard** — Compare against current state before calling Zustand setters in `onSelectionChange` per FE-RF-004.
- **Non-select filtering** — Filter `type === "select"` out of `onNodesChange`/`onEdgesChange` before applying changes per FE-RF-003.