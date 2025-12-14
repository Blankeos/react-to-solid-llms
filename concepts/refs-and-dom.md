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

## Measuring the DOM

When you need to measure an element (e.g., `getBoundingClientRect`) immediately after it updates, React uses `useLayoutEffect`. In Solid, `createEffect` runs after the DOM update paint, which is usually sufficient and safer.

### React: `useLayoutEffect`
```tsx
useLayoutEffect(() => {
  const { height } = ref.current.getBoundingClientRect();
  setHeight(height);
}, []);
```

### Solid: `createEffect`
```tsx
createEffect(() => {
  // This runs after the DOM has updated
  if (ref) {
    const { height } = ref.getBoundingClientRect();
    // Use it...
  }
});
```
If you absolutely need it *before* the browser paints (but after DOM mutation), Solid's `createRenderEffect` is closer to `useLayoutEffect`, but `createEffect` is the standard recommendation for most measurement needs to avoid blocking the main thread.

## Forwarding & Merging Refs

In React, you use `useImperativeHandle` or `forwardRef` to expose internal nodes or handle multiple refs. In Solid, because refs are just function callbacks (setters), you can easily compose them.

**React:**
```tsx
const ref = useRef();
useImperativeHandle(outerRef, () => ({ ... }));
```

**Solid:**
```tsx
const handleRef = (el) => {
  internalRef = el; // Keep a local reference
  props.ref?.(el);  // Forward to parent
};

<div ref={handleRef} />
```

## Custom Elements & Attributes

When working with Web Components, you often need to ensure values are set as HTML attributes (strings) rather than DOM properties.

**React:**
Often requires `useEffect` and `setAttribute` manually (prior to React 19).

**Solid:**
Use the `attr:` namespace directive to force attribute usage.

```tsx
<my-element 
  attr:data-index={index()} 
  attr:will-change={active() ? "transform" : undefined}
/>
```
