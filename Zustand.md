# Zustand - Complete Guide

## What is Zustand?

Zustand (German for "state") is a small, fast, and scalable state management solution for React. It is a simplified version of redux

### Quick Example

```typescript
import { create } from 'zustand'

type State = {
  count: number
}

type Actions = {
  increment: (qty: number) => void
  decrement: (qty: number) => void
}

const useCountStore = create<State & Actions>()((set) => ({
  count: 0,
  increment: (qty: number) => set((state) => ({ count: state.count + qty })),
  decrement: (qty: number) => set((state) => ({ count: state.count - qty })),
}))

function Counter() {
  const count = useCountStore((state) => state.count)
  return <h1>{count}</h1>
}

function Controls() {
  const increasePopulation = useCountStore((state) => state.increment)
  return <button onClick={increasePopulation}>one up</button>
}
```

**Key Points:**

- With types, recommend separating `State` and `Actions` to isolate variables and methods
- The store hook can be called with a `selector` function: `useCountStore((state) => state.count)`
- Only components using selected state will re-render when that state changes
- Actions are just functions that call `set` to update state

## Why Zustand?

- **Avoid prop drilling**: Share state across components without passing props through multiple levels
- **Simplify Redux flow**: No reducers, actions, or dispatch - just direct state updates
- **No Context Provider needed**: Works without wrapping your app in providers
- **Better performance**: Uses selectors to prevent unnecessary re-renders
- **Works outside React**: Can be used in vanilla JS/TS applications

## Core Concepts

### 1. Store Creation

A Zustand store is created using the `create` function. It takes a function that receives `set` (and optionally `get`) and returns the initial state and actions.

```typescript
const useCountStore = create<State & Actions>()((set, get) => ({
  count: 0,
  increment: (qty: number) => {
    const currentCount = get().count
    if (currentCount < 100) {
      set((state) => ({ count: state.count + qty }))
    }
  },
  decrement: (qty: number) => set((state) => ({ count: state.count - qty })),
}))
```

### 2. Selectors

Selectors allow you to subscribe to specific parts of the store, preventing unnecessary re-renders when unrelated state changes.

```typescript
// ✅ Good: Only subscribes to count
const count = useCountStore((state) => state.count)

// ✅ Good: Only subscribes to increment function (stable reference)
const increment = useCountStore((state) => state.increment)

// ✅ Good: Use the dedicated `useShallow` hook (Zustand v4.4+)
import { useShallow } from 'zustand/react/shallow'
const { count: safeCount, increment: safeIncrement } = useCountStore(
  useShallow((state) => ({ count: state.count, increment: state.increment }))
)

// ✅ Or use multiple atomic selectors:
const count = useCountStore((state) => state.count)
const increment = useCountStore((state) => state.increment)

// ❌ Bad: Subscribes to entire state, re-renders on any change
const store = useCountStore()

// ❌ Bad: Creates new object on every render, causes infinite updates
const { count, increment } = useCountStore((state) => ({
  count: state.count,
  increment: state.increment
}))
```

### 3. Actions

Actions are functions that update the store state using the `set` function.

#### 3.1 State Merging

Zustand automatically merges state updates. You don't need to spread the entire state:

```typescript
// ✅ Good: Zustand merges automatically
set((state) => ({ count: state.count + 1 }))

// ✅ Also works: Explicit merge (but unnecessary)
set((state) => ({ ...state, count: state.count + 1 }))
```

**For nested objects**, you need to merge manually:

```typescript
type State = {
  user: {
    name: string
    age: number
  }
}

// ✅ Good: Merge nested object
set((state) => ({
  user: { ...state.user, name: 'John' }
}))

// ❌ Bad: Loses age property
set((state) => ({
  user: { name: 'John' }
}))
```

## Best Practices

### 1. Keep Stores Small

Split large stores into smaller, focused stores or use the slice pattern (see below).

### 2. Reference Actions as Functions

When selecting actions, reference them directly to avoid unnecessary re-renders:

```typescript
// ✅ Good: Stable function reference
const increment = useCountStore((state) => state.increment)

// ❌ Bad: Creates new function reference
const increment = () => useCountStore.getState().increment()
```

### 3. Using Immer for Complex Updates

For deeply nested state, use Immer to write simpler update logic:

```typescript
import { produce } from 'immer'

type State = {
  deep: {
    nested: {
      obj: {
        count: number
      }
    }
  }
}

const useStore = create<State & { immerInc: () => void }>()((set) => ({
  deep: {
    nested: {
      obj: {
        count: 0
      }
    }
  },
  immerInc: () =>
    set(produce((state: State) => {
      ++state.deep.nested.obj.count
    })),
}))
```

### 4. Disable Merging (Replace Entire State)

To replace the entire state instead of merging:

```typescript
// Replace entire state (disable merging)
set(newState, true)

// Or use replace: true in the second parameter
set(newState, { replace: true })
```

## Advanced Patterns

### Pattern 1: Actions Outside the Store

You can define actions outside the store using `setState`:

```typescript
export const useBoundStore = create(() => ({
  count: 0,
  text: 'hello',
}))

// Actions defined outside the store
export const inc = () =>
  useBoundStore.setState((state) => ({ count: state.count + 1 }))

export const setText = (text: string) =>
  useBoundStore.setState({ text })

// Usage in components
function Component() {
  const count = useBoundStore((state) => state.count)

  return (
    <div>
      <p>{count}</p>
      <button onClick={inc}>Increment</button>
      {/* No need to use hook for actions */}
    </div>
  )
}
```

**Advantages:**

- Actions don't require a hook to call
- Facilitates code splitting
- Actions can be imported separately
- Useful for actions that don't need to be part of the store type

**Disadvantages:**

- Less type-safe (actions not part of store type)
- Can't use `get()` inside actions easily

### Pattern 2: Slice Pattern

The slice pattern allows you to split a large store into smaller, manageable pieces. Each slice is a function that receives `set` and `get` and returns a partial store.

**Benefits:**

- Organize related state and actions together
- Reuse slices across different stores
- Easier to maintain and test
- Better code splitting

**Example:**

```typescript
// fishSlice.ts
export const createFishSlice = (set: any) => ({
  fishes: 0,
  addFish: () => set((state: any) => ({ fishes: state.fishes + 1 })),
  removeFish: () => set((state: any) => ({ fishes: state.fishes - 1 })),
})

// bearSlice.ts
export const createBearSlice = (set: any) => ({
  bears: 0,
  addBear: () => set((state: any) => ({ bears: state.bears + 1 })),
  removeBear: () => set((state: any) => ({ bears: state.bears - 1 })),
})

// Combined slice that uses other slices
export const createBearFishSlice = (set: any, get: any) => ({
  addBearAndFish: () => {
    get().addBear()
    get().addFish()
  },
})
```

**Combining slices:**

```typescript
// store.ts
import { create } from 'zustand'
import { createBearSlice } from './bearSlice'
import { createFishSlice } from './fishSlice'
import { createBearFishSlice } from './bearFishSlice'

export const useBoundStore = create((...a) => ({
  ...createBearSlice(...a),
  ...createFishSlice(...a),
  ...createBearFishSlice(...a),
}))
```

**Type-safe slices:**

```typescript
import { StateCreator } from 'zustand'

type FishSlice = {
  fishes: number
  addFish: () => void
}

type BearSlice = {
  bears: number
  addBear: () => void
}

const createFishSlice: StateCreator<FishSlice> = (set) => ({
  fishes: 0,
  addFish: () => set((state) => ({ fishes: state.fishes + 1 })),
})

const createBearSlice: StateCreator<BearSlice> = (set) => ({
  bears: 0,
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
})

export const useBoundStore = create<FishSlice & BearSlice>()((...a) => ({
  ...createFishSlice(...a),
  ...createBearSlice(...a),
}))
```

**Important:** Do not `persist` inside slices. Apply persistence at the combined store level:

```typescript
import { create } from 'zustand'
import { createBearSlice } from './bearSlice'
import { createFishSlice } from './fishSlice'
import { persist } from 'zustand/middleware'

export const useBoundStore = create(
  persist(
    (...a) => ({
      ...createBearSlice(...a),
      ...createFishSlice(...a),
    }),
    { name: 'bound-store' },
  ),
)
```

### Pattern 3: Auto-Generated Selectors

Create a helper function to auto-generate selectors for each store property:

```typescript
import { StoreApi, UseBoundStore } from 'zustand'

type WithSelectors<S> = S extends { getState: () => infer T }
  ? S & { use: { [K in keyof T]: () => T[K] } }
  : never

const createSelectors = <S extends UseBoundStore<StoreApi<object>>>(
  _store: S,
) => {
  const store = _store as WithSelectors<typeof _store>
  store.use = {}
  for (const k of Object.keys(store.getState())) {
    ;(store.use as any)[k] = () => store((s) => s[k as keyof typeof s])
  }

  return store
}
```

**Usage:**

```typescript
const useBearStoreBase = create(() => ({
  bears: 0,
  increment: () => {},
}))

const useBearStore = createSelectors(useBearStoreBase)

// Now you can use auto-generated selectors
const bears = useBearStore.use.bears()
const increment = useBearStore.use.increment()
```

### Pattern 4: Using `combine` Middleware

The `combine` middleware separates state and actions more explicitly:

```typescript
import { create } from 'zustand'
import { combine } from 'zustand/middleware'

const useBearStore = create(
  combine({ bears: 0 }, (set) => ({
    increase: (by: number) => set((state) => ({ bears: state.bears + by })),
  })),
)
```

This provides better TypeScript inference and clearer separation.

## Middleware

### Persist Middleware

Save store state to localStorage/sessionStorage:

```typescript
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

const useStore = create(
  persist(
    (set) => ({
      count: 0,
      increment: () => set((state) => ({ count: state.count + 1 })),
    }),
    {
      name: 'storage-key', // unique name for localStorage key
      // Optional: customize what gets persisted
      partialize: (state) => ({ count: state.count }), // only persist count
    }
  )
)
```

### DevTools Middleware

Connect to Redux DevTools:

```typescript
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useStore = create(
  devtools(
    (set) => ({
      count: 0,
      increment: () => set((state) => ({ count: state.count + 1 })),
    }),
    { name: 'MyStore' }
  )
)
```

### Combining Middleware

You can combine multiple middleware:

```typescript
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

const useStore = create(
  devtools(
    persist(
      (set) => ({
        count: 0,
        increment: () => set((state) => ({ count: state.count + 1 })),
      }),
      { name: 'storage-key' }
    ),
    { name: 'MyStore' }
  )
)
```

## Vanilla Store vs React Store

### Vanilla Store (`createStore`)

Use `createStore` when you need Zustand outside React:

```typescript
import { createStore } from 'zustand/vanilla'

const store = createStore((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// Use outside React
store.getState().count
store.getState().increment()

// Use inside React (need to create hook)
import { useStore } from 'zustand'

const count = useStore(store, (state) => state.count)
```

### React Store (`create`)

Use `create` for React components (most common):

```typescript
import { create } from 'zustand'

const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
}))

// Use in React components
const count = useStore((state) => state.count)
```

## Common Pitfalls & Solutions

### 1. Infinite Re-render Loop

**Problem:** Creating new objects in selectors causes infinite updates

```typescript
// ❌ Bad: Creates new object every render
const { count, text } = useStore((state) => ({
  count: state.count,
  text: state.text
}))
```

**Solution:** Use atomic selectors or shallow comparison

```typescript
// ✅ Good: Separate selectors
const count = useStore((state) => state.count)
const text = useStore((state) => state.text)

// ✅ Good: Shallow comparison
import { shallow } from 'zustand/shallow'
const { count, text } = useStore(
  (state) => ({ count: state.count, text: state.text }),
  shallow
)
```

### 2. Stale Closures

**Problem:** Actions capture old state values

```typescript
// ❌ Bad: May use stale state
const increment = () => {
  setTimeout(() => {
    set((state) => ({ count: state.count + 1 })) // state might be stale
  }, 1000)
}
```

**Solution:** Use `get()` to read current state

```typescript
// ✅ Good: Always gets current state
const increment = () => {
  setTimeout(() => {
    const currentCount = get().count
    set({ count: currentCount + 1 })
  }, 1000)
}
```

### 3. Over-subscribing

**Problem:** Component re-renders when unrelated state changes

```typescript
// ❌ Bad: Subscribes to entire store
const store = useStore()
```

**Solution:** Use specific selectors

```typescript
// ✅ Good: Only subscribes to count
const count = useStore((state) => state.count)
```

## Complete Example: Todo App

Here's a complete example putting it all together:

```typescript
// types.ts
export type Todo = {
  id: string
  text: string
  completed: boolean
}

// store.ts
import { create } from 'zustand'
import { devtools, persist } from 'zustand/middleware'

type TodoState = {
  todos: Todo[]
  filter: 'all' | 'active' | 'completed'
}

type TodoActions = {
  addTodo: (text: string) => void
  toggleTodo: (id: string) => void
  deleteTodo: (id: string) => void
  setFilter: (filter: 'all' | 'active' | 'completed') => void
  clearCompleted: () => void
}

export const useTodoStore = create<TodoState & TodoActions>()(
  devtools(
    persist(
      (set) => ({
        // State
        todos: [],
        filter: 'all',

        // Actions
        addTodo: (text: string) =>
          set((state) => ({
            todos: [
              ...state.todos,
              {
                id: Date.now().toString(),
                text,
                completed: false,
              },
            ],
          })),

        toggleTodo: (id: string) =>
          set((state) => ({
            todos: state.todos.map((todo) =>
              todo.id === id ? { ...todo, completed: !todo.completed } : todo
            ),
          })),

        deleteTodo: (id: string) =>
          set((state) => ({
            todos: state.todos.filter((todo) => todo.id !== id),
          })),

        setFilter: (filter: 'all' | 'active' | 'completed') =>
          set({ filter }),

        clearCompleted: () =>
          set((state) => ({
            todos: state.todos.filter((todo) => !todo.completed),
          })),
      }),
      { name: 'todo-storage' }
    ),
    { name: 'TodoStore' }
  )
)

// Selectors (computed values)
export const useFilteredTodos = () => {
  const todos = useTodoStore((state) => state.todos)
  const filter = useTodoStore((state) => state.filter)

  return todos.filter((todo) => {
    if (filter === 'active') return !todo.completed
    if (filter === 'completed') return todo.completed
    return true
  })
}

// Component.tsx
function TodoApp() {
  const addTodo = useTodoStore((state) => state.addTodo)
  const setFilter = useTodoStore((state) => state.setFilter)
  const filter = useTodoStore((state) => state.filter)
  const filteredTodos = useFilteredTodos()

  return (
    <div>
      <TodoInput onAdd={addTodo} />
      <TodoFilter filter={filter} onFilterChange={setFilter} />
      <TodoList todos={filteredTodos} />
    </div>
  )
}

function TodoItem({ todo }: { todo: Todo }) {
  // Only subscribe to actions, not state
  const toggleTodo = useTodoStore((state) => state.toggleTodo)
  const deleteTodo = useTodoStore((state) => state.deleteTodo)

  return (
    <div>
      <input
        type="checkbox"
        checked={todo.completed}
        onChange={() => toggleTodo(todo.id)}
      />
      <span>{todo.text}</span>
      <button onClick={() => deleteTodo(todo.id)}>Delete</button>
    </div>
  )
}
```

## Quick Reference

### Creating a Store

```typescript
import { create } from 'zustand'

const useStore = create((set, get) => ({
  // state
  count: 0,
  // actions
  increment: () => set((state) => ({ count: state.count + 1 })),
}))
```

### Using in Components

```typescript
// Select state
const count = useStore((state) => state.count)

// Select action
const increment = useStore((state) => state.increment)

// Multiple values (use shallow)
import { shallow } from 'zustand/shallow'
const { count, text } = useStore(
  (state) => ({ count: state.count, text: state.text }),
  shallow
)
```

### Updating State

```typescript
// Simple update
set({ count: 5 })

// Functional update
set((state) => ({ count: state.count + 1 }))

// Read current state
const currentCount = get().count

// Replace entire state
set(newState, true)
```

### Outside React

```typescript
// Get state
const count = useStore.getState().count

// Update state
useStore.setState({ count: 5 })

// Subscribe
const unsubscribe = useStore.subscribe((state) => {
  console.log('State changed:', state)
})
```

## Resources

- [Official Documentation](https://zustand.docs.pmnd.rs/)
- [Flux-Inspired Practice Guide](https://zustand.docs.pmnd.rs/guides/flux-inspired-practice)
- [Zustand with Context API – An Advanced Pattern](https://www.youtube.com/watch?v=1Fi4hK7L1ec)
- [5 Zustand BEST Practices in 5 Minutes](https://www.youtube.com/watch?v=6tEQ1nJZ51w)
- [Zustand - Complete Tutorial](https://www.youtube.com/watch?v=_ngCLZ5Iz-0)
- [Working with Zustand (tkdodo)](https://tkdodo.eu/blog/working-with-zustand)
- [Zustand and React Context (tkdodo)](https://tkdodo.eu/blog/zustand-and-react-context)
- [Combining Zustand with React Query](https://www.youtube.com/watch?v=QTZTUrAbjeo)
