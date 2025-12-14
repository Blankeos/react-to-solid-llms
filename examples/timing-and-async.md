# Timing & Async: useDebouncedValue

## React Implementation
```typescript
export function useDebouncedValue<T = any>(
  value: T,
  wait: number,
  options = { leading: false }
): [T, () => void] {
  const [_value, setValue] = useState(value);
  // Refs for mutable state that doesn't trigger render
  const timeoutRef = useRef<number | null>(null);
  const cooldownRef = useRef(false);

  // Effect tracks 'value' change
  useEffect(() => {
    if (mountedRef.current) {
      if (!cooldownRef.current && options.leading) {
        // ...
      } else {
        timeoutRef.current = window.setTimeout(() => {
           setValue(value);
        }, wait);
      }
    }
  }, [value, options.leading, wait]);

  return [_value, cancel];
}
```

## Solid Implementation
```typescript
import { Accessor, createEffect, createSignal, on } from 'solid-js';

export function useDebouncedValue<T = any>(
  value: Accessor<T>, // Takes an Accessor
  wait: number,
  options = { leading: false },
) {
  const [_value, setValue] = createSignal<T>(value());
  
  // Just variables! No useRef needed.
  let timeoutRef: number | null = null;
  let cooldownRef = false;

  const cancel = () => window.clearTimeout(timeoutRef!);

  createEffect(
    // Explicit 'on' dependency. 
    // effectively "useEffect(..., [value])"
    on(value, () => {
      if (!cooldownRef && options.leading) {
        cooldownRef = true;
        setValue(_ => value());
      } else {
        cancel();
        timeoutRef = window.setTimeout(() => {
          cooldownRef = false;
          setValue(_ => value());
        }, wait);
      }
    }),
  );

  return [_value, cancel] as const;
}
```

## Key Takeaways
1.  **Variables**: Replaced `useRef` with simple `let` variables.
2.  **Explicit Tracking**: Used `on(value, ...)` to ensure the effect only runs when the input signal changes, not when other internal signals might be read (though `createSignal` reads are fine, `on` makes the intent "watch this" very clear).
3.  **Accessors**: `value` input is an Accessor.
