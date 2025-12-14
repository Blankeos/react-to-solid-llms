# Non-Reactive State

## `useRef` vs `let`

### React: `useRef` for Values
In React, because the component function re-runs on every render, local variables reset. To keep a value stable across renders without triggering a re-render, you use `useRef`.

```tsx
// React
function Timer() {
  const intervalId = useRef(null);

  useEffect(() => {
    intervalId.current = setInterval(...);
    return () => clearInterval(intervalId.current);
  }, []);
}
```

### Solid: Just use `let`
In Solid, the component function runs **once** (setup phase). Therefore, a standard JavaScript `let` variable persists for the life of the component.

```tsx
// Solid
function Timer() {
  let intervalId;

  onMount(() => {
    intervalId = setInterval(...);
  });

  onCleanup(() => {
    clearInterval(intervalId);
  });
}
```

## When to use Signals vs Variables?

| Use Case | React | Solid |
| :--- | :--- | :--- |
| **UI State** (Changes update DOM) | `useState` | `createSignal` |
| **DOM Reference** | `useRef` | `let` (or localized variable) |
| **Mutable Value** (Timers, flags) | `useRef` | `let` |
| **Derived Value** | `useMemo` | `createMemo` |

## Common Patterns

### Instance Variables
If you are porting a Class Component that had `this.lastScrollTop`, you can simply declare `let lastScrollTop = 0;` at the top of your Solid component function.

### Debounce/Throttle Timers
```tsx
function Search() {
  let timeout;

  const handleInput = (e) => {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      // Perform search
    }, 300);
  };

  return <input onInput={handleInput} />;
}
```
