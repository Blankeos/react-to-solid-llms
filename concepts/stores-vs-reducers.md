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
