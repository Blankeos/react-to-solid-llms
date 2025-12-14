# Effects & Lifecycle

## Dependency Arrays vs Auto-Tracking

### React: Manual Dependencies
React requires you to list every value used inside the effect.

```tsx
useEffect(() => {
  console.log(count);
}, [count]); // Forgot 'count'? Stale closure bug!
```

### Solid: Auto-Tracking
Solid's `createEffect` automatically subscribes to any signal read synchronously during its execution.

```tsx
createEffect(() => {
  console.log(count()); // Automatically tracks 'count'
});
```

## Cleanup

### React
Return a function from the effect.

```tsx
useEffect(() => {
  const timer = setInterval(...);
  return () => clearInterval(timer);
}, []);
```

### Solid
Use `onCleanup`.

```tsx
createEffect(() => {
  const timer = setInterval(...);
  onCleanup(() => clearInterval(timer));
});
```

## Explicit Dependencies (`on`)

Sometimes you want to be explicit, like React. Solid provides `on`.

```tsx
import { on, createEffect } from 'solid-js';

createEffect(on(count, (c) => {
  console.log('Count changed to', c);
  // Does NOT track anything inside here, only 'count'
}));
```
