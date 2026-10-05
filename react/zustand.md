**Table of Contents**

- [Zustand](#zustand)
- [Only export custom hooks](#only-export-custom-hooks)
- [Separate Actions from State](#separate-actions-from-state)
- [Model Actions as Events, not Setters](#model-actions-as-events-not-setters)

[<sub>Source</sub>](https://tkdodo.eu/blog/working-with-zustand)

---

# Zustand

**Zustand** is a small state management library that uses a single store defined with a simple function, without requiring reducers or actions like Redux. Components can read and update the store directly through a hook, with minimal boilerplate. Its simplicity and small bundle size make it a common choice for apps that outgrow plain Context.

# Only export custom hooks

```tsx
// ⬇️ not exported, so that no one can subscribe to the entire store
const useBearStore = create((set) => ({
  bears: 0,
  fish: 0,
  increasePopulation: (by) =>
    set((state) => ({ bears: state.bears + by })),
  eatFish: () => set((state) => ({ fish: state.fish - 1 })),
  removeAllBears: () => set({ bears: 0 }),
}))

// 💡 exported - consumers don't need to write selectors
export const useBears = () => useBearStore((state) => state.bears)
```

They’ll give you a cleaner interface, and you don’t need to write the selector repeatedly everywhere you want to subscribe to just one value in the store. Also, it avoids accidentally subscribing to the entire store:

```tsx
// ❌ we could do this if useBearStore was exported
const { bears } = useBearStore()
```

# Separate Actions from State

Actions are functions which update values in your store. These are static and never change, so they aren’t technically “state”. Organising them into a separate object in our store will allow us to expose them as a single hook to be used in any our components without any impact on performance:

```tsx
const useBearStore = create((set) => ({
  bears: 0,
  fish: 0,
  // ⬇️ separate "namespace" for actions
  actions: {
    increasePopulation: (by) =>
      set((state) => ({ bears: state.bears + by })),
    eatFish: () => set((state) => ({ fish: state.fish - 1 })),
    removeAllBears: () => set({ bears: 0 }),
  },
}))

export const useBears = () => useBearStore((state) => state.bears)
export const useFish = () => useBearStore((state) => state.fish)

// 🎉 one selector for all our actions
export const useBearActions = () =>
  useBearStore((state) => state.actions)
```

# Model Actions as Events, not Setters

This is a general tip, no matter if you’re working with useReducer (opens in a new window), Redux or Zustand. In fact, this tip is straight from the magnificent Redux style guide (opens in a new window). It will help you keep your business logic inside your store, and not in your components.

The examples above have already been using this pattern - the logic (e.g. “increase population”) lives in the store. The component just calls the action, and the store decides what to do with it.