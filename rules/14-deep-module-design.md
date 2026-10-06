# 14. Deep module design

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
- **Robust over shallow:** A shallow module that solves the happy path but fails at edges is a hack. Design for the common failure modes: null inputs, empty collections, concurrent access, boundary conditions. If a module only works "in the common case," it's a technical debt trap — the deletion test will betray it later.

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