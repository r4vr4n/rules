### 10.5 Forms

| ID | Rule | Tag |
| ---- | ------ | ----- |
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