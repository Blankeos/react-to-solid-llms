# Refs & DOM

## React Refs
In React, `useRef` is an object `{ current: ... }` that persists across renders.

```tsx
const ref = useRef(null);
<div ref={ref} />
// Access via ref.current
```

## Solid Refs
In Solid, refs are just variables. Since the component runs once, a local variable is stable!

```tsx
let ref;
<div ref={ref} />
// Access via ref
```

## When to use Signals for Refs?
If you need to know *when* the ref is attached (e.g., for `useClickOutside` logic that depends on the element existing), you might use a callback ref or a signal.

```tsx
// Solid "Signal Ref" pattern
const [ref, setRef] = createSignal<HTMLElement | null>(null);

<div ref={setRef} /> // Pass the setter!
```

This is useful in hooks where the DOM node is a dependency for an effect.

## Porting `useRef` for Mutable Values
If you used `useRef` just to hold a mutable value that doesn't trigger updates (like a timer ID):

**React:**
```tsx
const timer = useRef(0);
timer.current = 123;
```

**Solid:**
```tsx
// Just use a variable!
let timer = 0;
timer = 123;
```
