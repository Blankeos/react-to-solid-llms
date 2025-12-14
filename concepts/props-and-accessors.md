# Props & Accessors

## The Most Common Pitfall

In React, you pass values. In Solid, you pass **getters** (Accessors) if you want reactivity to persist down the tree.

### React: Values are Just Values
```tsx
// React
function Child({ count }) {
  // 'count' is a number. 
  // When parent re-renders, Child re-renders with new number.
  return <div>{count}</div>;
}
```

### Solid: Destructuring Kills Reactivity
Because Solid components run once, `props` is a reactive object. Destructuring it accesses the value *at that moment*.

```tsx
// Solid - WRONG ❌
function Child(props) {
  const { count } = props; // Accessed once! Reactivity lost.
  return <div>{count}</div>; // Will never update
}

// Solid - CORRECT ✅
function Child(props) {
  return <div>{props.count}</div>; // Accesses property in the effect/template
}
```

## The "Accessor" Pattern for Hooks

When porting hooks, React hooks usually take values. Solid hooks should often take functions (Accessors).

#### React Hook
```tsx
// useMediaQuery(query: string)
const matches = useMediaQuery('(min-width: 768px)');
```

If `query` changes, React re-calls the hook.

#### Solid Hook
```tsx
// useMediaQuery(query: Accessor<string> | string)
const [query, setQuery] = createSignal('(min-width: 768px)');
const matches = useMediaQuery(query); // Pass the signal!
```

Inside `useMediaQuery`, we call `query()` to get the current value.

**Rule of Thumb:** If an argument might change over time, accept `Accessor<T>` or `MaybeAccessor<T>` (a utility type usually defined as `T | Accessor<T>`).

```typescript
import { Accessor } from 'solid-js';

// Solid implementation
function useSomething(value: Accessor<string> | string) {
  const getValue = () => typeof value === 'function' ? (value as Accessor<string>)() : value;
  
  createEffect(() => {
    console.log(getValue()); // Tracks changes!
  });
}
```
