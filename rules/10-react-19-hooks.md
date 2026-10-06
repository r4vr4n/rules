### 10.13 React 19 New Hooks

React 19 introduces several new hooks that simplify common patterns. Use these instead of hand-rolled solutions.

| Hook | Purpose | When to Use |
| ------ | --------- | ------------- |
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