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
| ------ | ------ | --------- |
| `fallbackRender` | `({ error, resetErrorBoundary }) => ReactNode` | Dynamic fallback with retry |
| `resetKeys` | `unknown[]` | Reset boundary when keys change (route params) |
| `onError` | `(error, info) => void` | Log to console/error service |
| `onReset` | `() => void` | Cleanup state on retry |
```