# State vs Signals

## The Mental Shift

### React: `useState`
React's state is tied to the component instance. Changing state triggers a re-render of the component.

```tsx
const [count, setCount] = useState(0);
// count is the value (0)
```

### Solid: `createSignal`
Signals are independent reactive primitives. They live outside the component tree's render cycle.

```tsx
const [count, setCount] = createSignal(0);
// count is a GETTER function (() => 0)
```

## Key Differences

1.  **Read Syntax**: You must call the signal to read it: `count()`.
2.  **Write Syntax**: Similar to React, passing a function `setCount(c => c + 1)` works.
3.  **Location**: Signals can be defined *outside* components (global state) without any extra libraries.

## Porting Example

#### React
```tsx
function Counter() {
  const [count, setCount] = useState(0);
  
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

#### Solid
```tsx
function Counter() {
  const [count, setCount] = createSignal(0);
  
  return <button onClick={() => setCount(count() + 1)}>{count()}</button>;
}
```
