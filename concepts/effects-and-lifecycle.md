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

````tsx
import { on, createEffect } from 'solid-js';

createEffect(on(count, (c) => {
  console.log('Count changed to', c);
  // Does NOT track anything inside here, only 'count'
}));

## Lifecycle: getSnapshotBeforeUpdate & componentDidUpdate

When porting complex animations (like FLIP) or logic that relies on measuring the DOM *before* and *after* updates, you need to map React's Class Component lifecycle methods to Solid's reactive primitives.

### React: `getSnapshotBeforeUpdate` & `componentDidUpdate`
React provides specific lifecycle methods to measure the DOM before the update and then run animations after.

### Solid: `createComputed` & `createEffect`
Solid's reactivity structure maps cleanly to these phases:

1. **`createComputed`** (Pre-Update / `getSnapshotBeforeUpdate`): Runs *synchronously* when dependencies change, but *before* the DOM updates (template bindings are effects). Use this to measure the "before" state (snapshot).
2. **`createEffect`** (Post-Update / `componentDidUpdate`): Runs *after* the DOM has been updated. Use this to measure the "after" state and trigger animations.

```tsx
createComputed(on(items, () => {
  // 1. Measure BEFORE DOM update (willUpdate)
  snapshot = element.getBoundingClientRect();
}));

createEffect(on(items, () => {
  // 2. Measure AFTER DOM update (didUpdate)
  const current = element.getBoundingClientRect();

  // 3. Invert & Play Animation
  const delta = snapshot.top - current.top;
  element.animate([...]);
}));
````

## Accessing Previous Values

In React, `componentDidUpdate` gives you `prevProps` and `prevState`. In functional components, you often simulate this with a `usePrevious` hook using `useRef`.

**Solid's `createEffect` and `createComputed` receive the return value of their previous execution as an argument.** This allows you to track state transitions without external variables.

```tsx
// React
useEffect((prevCount) => {
  if (prevCount !== count) { ... }
  return count;
}, [count]);

// Solid
createEffect((prevCount) => {
  const current = count(); // unwrap first
  if (prevCount !== current) { ... }
  return current;
}, 0); // Initial value
```

If you need to access "previous props" (which might contain a mix of static values and signals) inside an effect, you should unwrap all signals so you can return a snapshot object.

```tsx
createEffect((prevProps) => {
  // Use prevProps...

  // Return current state for next run
  return {
    ...props,
    someSignal: someSignal(), // Unwrap signals to store their current value!
  };
});
```
