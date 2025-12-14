# Functional Recursion & Dynamic Wrapping

## Context

Dynamically wrapping components (like nested layouts) in SolidJS differs significantly from React due to the "run-once" nature of Solid components.

## The Challenge

In React, you can loop through a list of components and wrap them:

```tsx
function PageRenderer(props: React.FC<PropsWithChildren>) {
  const [layouts, setLayouts] = useState([RootLayout, DashboardLayout]);
 
  let page = props.children;
  layouts.forEach((Layout) => {
    page = <Layout>{page}</Layout>; // You just need to keep wrapping page with the Layout.
  });
 
  return page;
}
```

In SolidJS, this doesn't work because the function body executes only once. Reactive updates to the list of layouts wouldn't trigger a re-composition in the same way, and naively re-rendering everything is inefficient.

## Solution 1: Functional Recursion

Using `createComponent` and recursive getters to lazily evaluate children.

```tsx
import { createComponent, JSX } from "solid-js";
import { createStore } from "solid-js/store";

function PageRenderer(props: { children: JSX.Element }) {
  const [layouts, setLayouts] = createStore([
    RootLayout,
    DashboardLayout,
  ]);
 
  function renderLayouts(index: number = 0) {
    let layout = layouts[index];
 
    if (!layout) return props.children; // Base case. (Terminates here).
 
    return createComponent(layout, {
      get children(): JSX.Element {
        return renderLayouts(index + 1); // Recursive case.
      },
    });
  }
 
  return <>{renderLayouts(0)}</>;
}
```

This approach is efficient because:
- Thanks to `get children()`, the components only need to render when they need to (i.e. when layouts changes).
- `renderLayouts(0)` only gets called once. Reactivity is handled by the getters.

## Solution 2: Switch/Match Recursion

Using `Switch` and `Match` to simulate early returns and recursion.

```tsx
import { Switch, Match, JSX } from "solid-js";
import { createStore } from "solid-js/store";

// Helper component that handles wrapping using Switch/Match
const WrapWithLayouts = (props: { layouts: any[], children: JSX.Element, index: number }) => {
  return (
    <Switch fallback={props.children}>
      <Match when={props.index < props.layouts.length}>
        {(() => {
          const CurrentComponent = props.layouts[props.index];
          return (
            <CurrentComponent>
              <WrapWithLayouts layouts={props.layouts} index={props.index + 1}>
                {props.children}
              </WrapWithLayouts>
            </CurrentComponent>
          );
        })()}
      </Match>
    </Switch>
  );
};
 
function PageRenderer() {
  const [layouts, setLayouts] = createStore([
    RootLayout,
    DashboardLayout,
  ]);
 
  return <WrapWithLayouts layouts={layouts} index={0}>I am page</WrapWithLayouts>
}
```

This demonstrates how you can simulate "early returns" in SolidJS using `Switch` and `Match` with a fallback.
