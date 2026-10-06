### Keyboard Navigation & Shortcuts Best Practices

**Keyboard Navigation (FE-A11Y-003 compliance):**

- **Tab order matches visual order** — Verify that the visual tab order matches the logical navigation order. Check what sits between expected tab stops.
- **Focus-reachable affordances** — Every mouse affordance (hover states, dropdowns, modals) must have a keyboard equivalent. Pair `:hover` with `:focus-within` / `:focus-visible` in CSS.
- **Escape key** — Escape should do something sensible where it applies (progressive Escape: first clears, second blurs/closes). Never leave focus in a state where Escape has no effect.
- **Focus management** — When opening modals/dialogs, focus should be trapped inside the modal and return to the previous focus element on close.
- **Landmark regions** — Use semantic HTML (`<nav>`, `<main>`, `<section>`, `<header>`, `<footer>`) with MUI's `component` prop for screen reader navigation.
- **Skip links** — Provide a "Skip to main content" link as the first focusable element for keyboard users.

**Keyboard Shortcuts:**

- **Avoid `tabIndex > 0`** — Never use `tabIndex` greater than 0 (FE-A11Y-004). If an element needs to be focusable, it should be naturally focusable (button, link, input) or use `role="dialog"` with proper alertdialog patterns.
- **Explicit shortcuts** — Document keyboard shortcuts clearly. Use `aria-label` on icon-only buttons (`<IconButton aria-label="Delete row">`).
- **No conflicting shortcuts** — New global shortcuts must not collide with existing ones. Search existing keydown listeners and make shortcuts inert when target is off-screen.
- **Visual focus indicator** — Ensure `outline` or `:focus-visible` styles are present so keyboard users can see where focus is.
- **Modifier key combinations** — When using `Cmd/Ctrl + X` shortcuts, ensure they don't conflict with browser native shortcuts. Provide a way to disable or customize them.

**Forms & Inputs:**

- **Accessible labels** — Every interactive element must have an accessible label via `aria-label` or visible text (FE-A11Y-001).
- **Form field association** — Use `<label>` elements connected to inputs via `for`/`id`, or `aria-labelledby` for composite widgets.
- **Error identification** — Programmatically associate error messages with fields using `aria-describedby` or `form.errorMessage` from TanStack Form.
- **Disabled state** — Use native `disabled` attribute on form elements rather than `tabIndex=-1` or CSS-only disabling.

**React Flow Specific:**

- **Handle visibility** — Hide handles with `visibility: hidden` (not `display: none`) per FE-RF-002.
- **Selection change guard** — Compare against current state before calling Zustand setters in `onSelectionChange` per FE-RF-004.
- **Non-select filtering** — Filter `type === "select"` out of `onNodesChange`/`onEdgesChange` before applying changes per FE-RF-003.