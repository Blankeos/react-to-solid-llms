# Lists, Keys & Referential Integrity

## The `key` Prop vs `<For>`

### React: `map` + `key`
In React, you render lists by mapping over an array and providing a `key` prop so React can identify which items changed, added, or removed.

```tsx
// React
return (
  <ul>
    {items.map((item) => (
      <li key={item.id}>{item.text}</li>
    ))}
  </ul>
);
```

### Solid: `<For>` Component
Solid uses the `<For>` component for rendering lists. It automatically handles efficient updates and referential integrity based on the data items themselves. You generally **do not need a `key` prop**.

```tsx
// Solid
import { For } from 'solid-js';

return (
  <ul>
    <For each={items()}>{(item, index) =>
      <li>{item.text}</li>
    }</For>
  </ul>
);
```

## Referential Integrity & Reactivity

### The Problem with `createSignal([ ... ])`
If you use a simple signal for an array and update it immutably (like React), Solid sees a "new array."

```tsx
const [items, setItems] = createSignal([{ id: 1 }, { id: 2 }]);

// ⚠️ This causes ALL items to re-render because the array reference changed!
setItems([...items(), { id: 3 }]);
```

Because Solid components run once, "re-rendering" a list means unmounting every single DOM node in the list and recreating them. This destroys local state (like focus or animations) and is poor for performance.

### The Solution: `createStore`
`createStore` provides granular mutations. When you use `setStore` to append an item, Solid knows *only* that index changed or added, and updates the DOM surgically.

```tsx
import { createStore } from 'solid-js/store';

const [state, setState] = createStore({
  items: [{ id: 1, text: "A" }]
});

// ✅ Only appends one node. Existing nodes stay touched.
setState("items", items => [...items, { id: 2, text: "B" }]);
```

## Advanced: `reconcile` for Bulk Updates
Sometimes you receive a whole new array from an API, but you want to preserve the identity of existing items (to keep DOM nodes alive, animations running, etc.).

`reconcile` matches items by ID (or reference) and merges the changes.

```tsx
import { createStore, reconcile } from 'solid-js/store';

// Assume current state: [{ id: 1, val: 10 }, { id: 2, val: 20 }]
// API returns: [{ id: 1, val: 10 }, { id: 2, val: 25 }, { id: 3, val: 30 }]

// Using simple setStore would replace the objects.
// Using reconcile updates 'val' on id:2, keeps id:1 as-is, adds id:3.
setState("items", reconcile(newItems));
```

This is crucial for implementing things like **Sortable Lists** or live-updating feeds where you don't want to flash the UI on every update.
