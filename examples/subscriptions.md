# Subscriptions: useMediaQuery

## React Implementation
```typescript
export function useMediaQuery(
  query: string,
  // ... options
): boolean {
  const [matches, setMatches] = useState(getInitialValue(query));
  
  useEffect(() => {
    const mediaQuery = window.matchMedia(query);
    setMatches(mediaQuery.matches);
    
    const listener = (event) => setMatches(event.matches);
    mediaQuery.addEventListener('change', listener);
    return () => mediaQuery.removeEventListener('change', listener);
  }, [query]); // Re-runs if query string changes

  return matches;
}
```

## Solid Implementation
```typescript
import { Accessor, createEffect, createSignal } from 'solid-js';

export function useMediaQuery(
  query: Accessor<string>, // Accepts an Accessor!
  // ... options
) {
  const [matches, setMatches] = createSignal(getInitialValue(query()));

  let queryRef: MediaQueryList;

  createEffect(() => {
    // Calling query() tracks it. 
    // If the signal passed to query updates, this effect re-runs.
    if ('matchMedia' in window) {
      queryRef = window.matchMedia(query());
      setMatches(queryRef.matches);
      
      const listener = event => setMatches(event.matches);
      queryRef.addEventListener('change', listener);
      
      // Cleanup happens automatically before next run or unmount
      onCleanup(() => queryRef.removeEventListener('change', listener));
    }
  });

  return matches; // Return the signal (Accessor), not the value!
}
```

## Key Takeaways
1.  **Arguments**: `string` -> `Accessor<string>` to support dynamic queries.
2.  **Return**: Returns `matches` (the signal), allowing the consumer to track it purely.
3.  **Effect**: `createEffect` re-runs automatically when `query()` changes.
