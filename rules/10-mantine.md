### 10.14 Mantine Hooks (Copy-Paste Only)

**Rule:** Do not install `@mantine/hooks`. Copy needed hooks from Mantine source into `src/hooks/mantine/` — they are MIT-licensed, dependency-free, and solve SSR/cleanup edge cases correctly.

**Approved hooks to copy (reference: <https://github.com/mantinedev/mantine/tree/master/packages/@mantine/hooks/src>):**

| Hook | Use Case | Source File |
| ------ | ---------- | ------------- |
| `useDebouncedState` | Search inputs, auto-save | `use-debounced-state/use-debounced-state.ts` |
| `useDebouncedValue` | Debounce values with cancel/flush | `use-debounced-value/use-debounced-value.ts` |
| `useLocalStorage` | Persist state to localStorage (SSR-safe) | `use-local-storage/use-local-storage.ts` |
| `useSessionStorage` | Persist to sessionStorage | `use-session-storage/use-session-storage.ts` |
| `useMediaQuery` | Responsive breakpoints | `use-media-query/use-media-query.ts` |
| `useHotkeys` | Global keyboard shortcuts | `use-hotkeys/use-hotkeys.ts` |
| `useClickOutside` | Dropdown/modal close on outside click | `use-click-outside/use-click-outside.ts` |
| `useViewportSize` | Window dimensions | `use-viewport-size/use-viewport-size.ts` |
| `useReducedMotion` | Respect `prefers-reduced-motion` | `use-reduced-motion/use-reduced-motion.ts` |
| `useIntersection` | Lazy load, infinite scroll | `use-intersection/use-intersection.ts` |
| `useScrollIntoView` | Smooth scroll to element | `use-scroll-into-view/use-scroll-into-view.ts` |
| `useClipboard` | Copy to clipboard | `use-clipboard/use-clipboard.ts` |
| `useHash` | URL hash state | `use-hash/use-hash.ts` |
| `useIdle` | User idle detection | `use-idle/use-idle.ts` |
| `useWindowEvent` | Typed window event listeners | `use-window-event/use-window-event.ts` |

**Copy pattern — create `src/hooks/mantine/use-debounced-state.ts`:**

```typescript
import { useCallback, useEffect, useRef, useState } from 'react';

export interface UseDebouncedStateOptions {
  leading?: boolean;
}

export type UseDebouncedStateReturnValue<T> = [T, (newValue: T | ((prev: T) => T)) => void];

export function useDebouncedState<T = any>(
  defaultValue: T,
  wait: number,
  options?: UseDebouncedStateOptions
): UseDebouncedStateReturnValue<T> {
  const [value, setValue] = useState(defaultValue);
  const timeoutRef = useRef<ReturnType<typeof setTimeout>>();
  const cooldownRef = useRef(false);
  const mountedRef = useRef(false);

  const cancel = useCallback(() => {
    if (timeoutRef.current) {
      clearTimeout(timeoutRef.current);
      timeoutRef.current = undefined;
    }
    cooldownRef.current = false;
  }, []);

  const flush = useCallback(() => {
    if (timeoutRef.current) {
      cancel();
      cooldownRef.current = false;
      setValue(value);
    }
  }, [cancel, value]);

  useEffect(() => {
    if (mountedRef.current) {
      if (!cooldownRef.current && options?.leading) {
        cooldownRef.current = true;
        setValue(value);
        timeoutRef.current = setTimeout(() => {
          cooldownRef.current = false;
        }, wait);
      } else {
        cancel();
        timeoutRef.current = setTimeout(() => {
          cooldownRef.current = false;
          setValue(value);
        }, wait);
      }
    }
  }, [value, options?.leading, wait, cancel]);

  useEffect(() => {
    mountedRef.current = true;
    return cancel;
  }, [cancel]);

  return [value, setValue];
}
```

**TypeScript types:** Copy `UseDebouncedStateOptions`, `UseDebouncedStateReturnValue` from source. Export them for consumers.

**Why copy-paste:** Zero dependencies, tree-shakable, no version drift, full TypeScript support, customizable.