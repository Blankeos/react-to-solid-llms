# Callbacks & Stability

## The "Run Once" Mental Model

In React, components re-run their entire function body on every render. This means any function defined inside the component is recreated every time.

```tsx
// React
function MyComponent() {
  // Re-created on every render
  const handleClick = () => console.log('click');
  
  return <button onClick={handleClick} />;
}
```

To prevent this causing unnecessary re-renders in children, React provides `useCallback`.

In **Solid**, components are just setup functions that run **once**.

```tsx
// Solid
function MyComponent() {
  // Created once, never recreated
  const handleClick = () => console.log('click');
  
  return <button onClick={handleClick} />;
}
```

## Why `useCallback` is redundant

Because `handleClick` is stable by definition (it's defined in a closure that only executes once), you almost never need to memoize functions.

### React Pattern
```tsx
const handleChange = useCallback((event) => {
  setValue(event.target.value);
}, []); // Dependency array needed to keep closure stale/fresh logic correct
```

### Solid Pattern
```tsx
// No hook needed. Accessing state (signals) works because 
// signals are stable references with dynamic values.
const handleChange = (event) => {
  setValue(event.target.value);
};
```

## When do you need memoization?

You only need `createMemo` (Solid's `useMemo`) for **derived values** that are expensive to compute. You rarely memoize *functions*.

If you need to pass a callback to a dependency array of an effect (rare in Solid due to auto-tracking), it will already be stable.
