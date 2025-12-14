# Event Listeners: useClickOutside

## React Implementation
```typescript
import { useEffect, useRef } from 'react';

export function useClickOutside<T extends HTMLElement = any>(
  callback: (event: EventType) => void,
  events?: string[] | null,
  nodes?: (HTMLElement | null)[]
) {
  const ref = useRef<T>(null);
  const eventsList = events || DEFAULT_EVENTS;

  useEffect(() => {
    const listener = (event: Event) => {
      // ... logic ...
      if (ref.current && !ref.current.contains(target as Node)) {
        callback(event as EventType);
      }
    };

    eventsList.forEach((fn) => document.addEventListener(fn, listener));

    // Cleanup returned from effect
    return () => {
      eventsList.forEach((fn) => document.removeEventListener(fn, listener));
    };
  }, [ref, callback, nodes]); // Dependencies trigger re-bind

  return ref;
}
```

## Solid Implementation
```typescript
import { createEffect, createSignal, onCleanup } from 'solid-js';

export function useClickOutside<T extends HTMLElement = any>(
  handler: () => void,
  events?: string[] | null,
  nodes?: (HTMLElement | null)[],
) {
  // Use a signal for the ref! 
  // This allows the effect to react when the ref is actually attached to a DOM node.
  const [ref, setRef] = createSignal<T>();

  createEffect(() => {
    const listener = (event: any) => {
      // ... logic ...
      // accessing ref() value
      if (ref() && !ref()!.contains(target)) {
        handler();
      }
    };

    (events || DEFAULT_EVENTS).forEach(fn => document.addEventListener(fn, listener));

    // onCleanup used inside the effect
    onCleanup(() => {
      (events || DEFAULT_EVENTS).forEach(fn => document.removeEventListener(fn, listener));
    });
  });

  return setRef; // Return the setter to be used as ref={setRef}
}
```

## Key Takeaways
1.  **Refs**: `useRef` -> `createSignal` (if you need to react to the element mounting).
2.  **Cleanup**: `return () => ...` -> `onCleanup(() => ...)`.
3.  **Ref usage**: `<div ref={ref} />` in React becomes `<div ref={setRef} />` in Solid when using this signal pattern.
