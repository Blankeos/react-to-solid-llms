# Simple State: useCounter

## React Implementation
```typescript
import { useCallback, useState } from 'react';
import { clamp } from '../utils';

export function useCounter(
  initialValue = 0,
  options?: UseCounterOptions
): [number, UseCounterHandlers] {
  const { min, max } = { ...DEFAULT_OPTIONS, ...options };
  const [count, setCount] = useState<number>(clamp(initialValue, min, max));

  // useCallbacks needed to maintain reference stability
  const increment = useCallback(
    () => setCount((current) => clamp(current + 1, min, max)),
    [min, max]
  );

  const decrement = useCallback(
    () => setCount((current) => clamp(current - 1, min, max)),
    [min, max]
  );

  const set = useCallback((value: number) => setCount(clamp(value, min, max)), [min, max]);

  const reset = useCallback(
    () => setCount(clamp(initialValue, min, max)),
    [initialValue, min, max]
  );

  return [count, { increment, decrement, set, reset }];
}
```

## Solid Implementation
```typescript
import { createSignal } from 'solid-js';
import { clamp } from '../utils/clamp';

export function useCounter(
  initialValue = 0,
  options: Partial<{ min: number; max: number }> = DEFAULT_OPTIONS,
) {
  // createSignal instead of useState
  const [count, setCount] = createSignal<number>(clamp(initialValue, options?.min, options?.max));

  // No useCallback needed! Functions are stable because the hook body runs once.
  // Note: We access options?.min inside the function, so it reads the *current* options reference
  // (though options themselves aren't reactive here unless passed as a signal/store, 
  // but if this hook is called once, options are fixed closure values anyway).
  const increment = () => setCount(current => clamp(current + 1, options?.min, options?.max));
  const decrement = () => setCount(current => clamp(current - 1, options?.min, options?.max));
  const set = (value: number) => setCount(clamp(value, options?.min, options?.max));
  const reset = () => setCount(clamp(initialValue, options?.min, options?.max));

  return [count, { increment, decrement, set, reset }] as const;
}
```

## Key Takeaways
1.  `useState` -> `createSignal`.
2.  `useCallback` -> Plain functions.
3.  Dependency arrays (`[min, max]`) disappear.
