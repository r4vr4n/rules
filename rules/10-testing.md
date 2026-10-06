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
```