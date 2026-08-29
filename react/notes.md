**Table of Contents**
- [Component Life Cycle](#notes--component-life-cycle)
- [Differences between refs and state](#notes--differences-between-refs-and-state)


---

<a name="notes--component-life-cycle" id="notes--component-life-cycle"></a>

## Component Life Cycle

React components have a lifecycle consisting of **three phases**: **Mounting**, **Updating**, and **Unmounting** along with several “lifecycle methods” that you can override to run code at particular times in the process.

It is **not recommended** to use lifecycle methods **manually**. Instead, use the useEffect hook with functional components.

[Read more...](https://react.dev/learn/lifecycle-of-reactive-effects)

---

<a name="notes--differences-between-refs-and-state" id="notes--differences-between-refs-and-state"></a>

## Differences between refs and state

| ref | state |
| --- | --- |
| `useRef(initialValue)` returns `{ current: initialValue }` | `useState(initialValue)` returns the current value of a state variable and a state setter function (`[value, setValue]`) |
| Doesn’t trigger re-render when you change it. | Triggers re-render when you change it. |
| Mutable—you can modify and update `current`’s value outside of the rendering process. | ”Immutable”—you must use the state setting function to modify state variables to queue a re-render. |
| You shouldn’t read (or write) the `current` value during rendering. | You can read state at any time. However, each render has its own [snapshot](https://react.dev/learn/state-as-a-snapshot) of state which does not change. |

<a name="notes--when-to-use-refs" id="notes--when-to-use-refs"></a>

### When to use refs

Typically, you will use a ref when your component needs to “step outside” React and communicate with external APIs—often a browser API that won’t impact the appearance of the component. Here are a few of these rare situations:

- Storing **timeout IDs**

- Storing and manipulating **DOM elements**

- Storing other **objects that aren’t necessary to calculate the JSX**

If your component needs to store some value, but it doesn’t impact the rendering logic, choose refs.

<a name="notes--summary" id="notes--summary"></a>

### Summary

Refs provide a way to access DOM nodes or React elements created in the render method.

In the typical React dataflow, props are the only way that parent components interact with their children. To modify a child, you re-render it with new props. However, there are a few cases where you need to imperatively modify a child outside of the typical dataflow. The child to be modified could be an instance of a React component, or it could be a DOM element. For both of these cases, React provides an escape hatch.

Treat refs as an **escape hatch** and **Don’t read or write** `ref.current` **during rendering**

The ref itself is a **regular JavaScript object**, and so it behaves like one.