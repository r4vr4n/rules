# 9. JavaScript / TypeScript practices

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
- Never `eval`; never build HTML from unsanitized user input (X
<tool_call>
- secrets never in client-reachable code

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