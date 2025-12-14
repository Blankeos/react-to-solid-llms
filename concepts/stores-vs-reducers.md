# Stores vs Reducers

## The Problem with `useState` / `useReducer`

In React, managing complex nested state often requires `useReducer` or careful shallow copying with `useState`.

```tsx
// React
const [state, dispatch] = useReducer(reducer, {
  count: 0,
  user: { name: "John" },
});
// or
setUser({ ...user, name: "Jane" });
```

## Enter `createStore`

Solid provides `createStore`, which offers a proxy-based API similar to **Immer**. It allows you to read nested state directly and write to it with fine-grained reactivity.

### Basic Mapping

| React Concept       | Solid Concept                         |
| :------------------ | :------------------------------------ |
| `useState` (object) | `createStore`                         |
| `useReducer`        | `createStore` (store actions pattern) |
| `Immer`             | Built-in to `createStore` setters     |

### Example: Nested Updates

#### React (Complex)

```tsx
const [state, setState] = useState({
  todos: [{ id: 1, text: "Learn React", completed: false }],
});

const toggleTodo = (id) => {
  setState((prev) => ({
    ...prev,
    todos: prev.todos.map((todo) =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo,
    ),
  }));
};
```

#### Solid (Simple)

```tsx
import { createStore } from "solid-js/store";

const [state, setState] = createStore({
  todos: [{ id: 1, text: "Learn Solid", completed: false }],
});

// "Mutate" deeply using path syntax
const toggleTodo = (id) => {
  setState(
    "todos",
    (todo) => todo.id === id,
    "completed",
    (c) => !c,
  );
};
```

## Replacing `useReducer`

Instead of a giant switch statement, you can just define functions that mutate the store.

```tsx
const [state, _setState] = createStore({ count: 0 });

const actions = {
  increment: () => _setState("count", (c) => c + 1),
  decrement: () => _setState("count", (c) => c - 1),
};
```

This provides the same "separation of concerns" benefits as Redux/useReducer without the boilerplate.

## Syncing with External Stores (Observer Pattern)

Sometimes your state lives outside the component tree (e.g., a global notification manager like Sonner). You can use `createStore` to efficiently sync with that external source.

### Example: Syncing a Toast Manager

Instead of forcing a full React re-render when a new toast is added, we can fine-grain the updates.

```tsx
import { createStore, reconcile } from 'solid-js/store';
import { ToastState } from './external-state'; // The vanilla JS observer

const [state, setState] = createStore({ toasts: [] });

onMount(() => {
  // Subscribe to the external vanilla JS store
  const unsubscribe = ToastState.subscribe((toast) => {
    // 1. Handle specialized events (like dismiss)
    if (toast.dismiss) {
      setState("toasts", (t) => t.filter(item => item.id !== toast.id));
      return;
    }

    // 2. Handle Add/Update
    // If the toast already exists, update it deeply.
    // If not, prepend it.
    const exists = state.toasts.find(t => t.id === toast.id);
    if (exists) {
      // Solid's reconcile or path syntax handles the merge seamlessly
      setState("toasts", (t) => t.id === toast.id, reconcile(toast));
    } else {
      setState("toasts", (t) => [toast, ...t]);
    }
  });

  onCleanup(unsubscribe);
});
```

By connecting an external vanilla JS "Observer" to a Solid Store, you get the benefits of a framework-agnostic core logic with the fine-grained reactivity of Solid's view layer.
