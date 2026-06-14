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
- store hook can be called with a `selector` function: `useCountStore((state) => state.count)`
- Only components using selected state will re-render when that state changes
- Actions are functions that call `set` to update state

## Why Zustand?

- **Avoid prop drilling**: Share state across components without passing props through multiple levels
- **Simplify Redux flow**: No reducers, actions, or dispatch - direct state updates
- **No Context Provider needed**: Works without wrapping your app in providers
- **Better performance**: Uses selectors to prevent unnecessary re-renders
- **Works outside React**: Can be used in vanilla JS/TS applications

## Core Concepts

### 1. Store Creation

Zustand store is created using the `create` function. It takes a function that receives `set` (and optionally `get`) and returns the initial state and actions.

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

When selecting actions, reference them directly to get a **stable function reference**:

```typescript
// ✅ Good: Stable reference — same function identity across renders
const increment = useCountStore((state) => state.increment)

// ❌ Bad: New arrow function on every render
const increment = () => useCountStore.getState().increment()
```

**Why the bad pattern is harmful:**

`() => useCountStore.getState().increment()` expression creates a **new arrow function every render**. This causes:

- **Broken memoization** — if passed to a `React.memo` child, the child re-renders because the prop (the new function) changes every time
- **Spurious effect re-runs** — if used in `useEffect`/`useCallback` dependencies, the effect fires on every render
- **Unnecessary child work** — even without memo, any component receiving the function as a prop allocates and garbage-collects the closure each frame

selector form `useCountStore((s) => s.increment)` returns the **same function reference** that was defined in the store creator, so it's stable for the entire lifetime of the store (unless the store itself replaces the action).

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

### 5. When to use `get()` / `getState()`

- **Inside the store definition (`create((set, get) => ...)`)**
  - Use `get()` when an action needs the *latest* state snapshot, especially for logic that depends on current values or runs asynchronously (to avoid stale closures).
  - Example: `const count = get().count` before deciding how to `set`.

- **Outside React (vanilla code, services, utilities)**
  - Use `useStore.getState()` (or `store.getState()` for vanilla stores) in non-React modules: API clients, event handlers, WebSocket callbacks, logging, etc.
  - This gives you the current state without subscribing anything to updates.

- **Inside React components (rarely)**
  - For rendering UI, **do not** rely on `getState()` because it doesn't subscribe the component to updates. Prefer `useStore((state) => state.someField)` or generated selectors.
  - Only reach for `getState()` in components for true one-off reads where you explicitly don’t want re-renders (which is uncommon).

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

slice pattern allows you to split a large store into smaller, manageable pieces. Each slice is a function that receives `set` and `get` and returns a partial store.

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

`combine` middleware separates state and actions more explicitly:

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

### `set` Gotchas

- **Always return an object:** `set((state) => ({ count: state.count + 3 }))` — returning a bare value is a no-op
- **Don't use `async` updaters:** `set(async (state) => ({ ... }))` doesn't work — do the async work outside `set`, then call `set` with the result
- **`set` only sets state:** use it to update values, not to call other store actions (call those directly)
- **Don't put promises in state:** resolve async data before calling `set`; storing a pending Promise breaks serialization and selectors
- **`set` is synchronous but re-renders are batched:** React may batch multiple `set` calls into a single render — rely on `get()` if you need the absolute latest state in a subsequent action

## Zustand vs Signals

### Preact Signals in React

Signals (via `@preact/signals-react`) take a different approach: **fine-grained reactivity at the value level**, not the component level.

```typescript
// Signals approach
import { signal, computed } from '@preact/signals-react'

const count = signal(0)
const doubled = computed(() => count.value * 2)

function Counter() {
  // No selector — auto-tracked at the .value read site
  return <p>{doubled.value}</p>
}

function Controls() {
  return <button onClick={() => count.value++}>+1</button>
}
```

```typescript
// Zustand equivalent
import { create } from 'zustand'

const useStore = create<{ count: number; doubled: number; inc: () => void }>()(
  (set) => ({
    count: 0,
    get doubled() {
      return this.count * 2
    },
    inc: () => set((s) => ({ count: s.count + 1 })),
  })
)

function Counter() {
  const doubled = useStore((s) => s.doubled)
  return <p>{doubled}</p>
}
```

### Key Differences

| Aspect | Zustand | Preact Signals |
| --- | --- | --- |
| **Reactivity model** | Subscribe + selector (component-level) | Push-based granular (value-level) |
| **Re-render scope** | Whole component re-renders | Pinpoint DOM updates (with Babel transform) |
| **Setup** | Zero config | Requires Babel transform or `useSignals()` call |
| **Outside React** | `getState()` / `subscribe()` | `.value` read + `effect()` |
| **Derived state** | Manual selector or `get` + computed | `computed()` — auto-tracking, no deps array |
| **Middleware** | persist, devtools, immer, etc. | None (bring your own) |
| **DevTools** | Redux DevTools via `devtools()` | None built-in |
| **Bundle** | ~1KB | ~3KB (signals-core + react adapter) |

### TC39 Signals (ECMAScript 2026)

[TC39 Signals Proposal](https://github.com/tc39/proposal-signals) reached Stage 4 and is part of ECMAScript 2026. Its primitives:

```typescript
// TC39 Signals (native)
const count = new Signal.State(0)
const doubled = new Signal.Computed(() => count.get() * 2)

new Signal.Effect(() => console.log('count:', count.get()))
```

This is **not a direct user-facing API** — it's designed for frameworks to build upon. Think of it like Promises: you rarely write `new Promise((resolve) => ...)` directly when using async/await. Similarly, React/Zustand/etc. would adopt TC39 signals under the hood.

**What changes for Zustand users?** Probably nothing directly. Zustand could internally use `Signal.State` for its store to achieve better interop, but the API you write (`create`, `set`, `get`) stays the same.

### When to use which

- **Zustand** — global app state, cross-component shared state, state with side-effect middleware (persist, devtools), vanilla JS modules
- **Signals** — high-frequency updates (animations, real-time data), you want automatic fine-grained reactivity, building a new project that can use the Babel transform
- **Both** — not uncommon. A signal-based derived value can read from a Zustand store via `subscribe`, and Zustand actions can write to signals.

---

## Zustand: Actual Pros and Cons (with Examples)

### ✅ Pros

#### 1. Tiny Bundle, Zero Config

```text
// package.json size comparison (gzipped)
zustand:      ~1 KB
@reduxjs/toolkit: ~11 KB
recoil:       ~19 KB
@preact/signals-react: ~3 KB
```

Just `npm install zustand` — no providers, no Babel plugins, no `index.tsx` changes.

#### 2. Works Outside React

This is the single biggest differentiator vs hooks-based solutions:

```typescript
// ✅ Zustand: Read/write from anywhere
import { useAuthStore } from './stores/auth'

// In an API interceptor
api.interceptors.response.use(
  (res) => res,
  (err) => {
    if (err.status === 401) {
      useAuthStore.getState().logout() // works outside React
      navigate('/login')
    }
    return Promise.reject(err)
  }
)

// In a WebSocket handler
ws.onmessage = (event) => {
  const data = JSON.parse(event.data)
  useChatStore.getState().addMessage(data) // no component needed
}
```

Signals can do this too (.value outside React), but Preact Signals for React needs the component to call `useSignals()` or use the Babel transform to track dependencies.

#### 3. Selector-Based Re-render Control

You decide exactly what a component subscribes to — no magic:

```typescript
const useStore = create(() => ({
  user: { name: 'Alice', avatar: 'alice.png' },
  notifications: [],
  theme: 'dark',
}))

// ✅ Only re-renders when user.name changes
const name = useStore((s) => s.user.name)

// ✅ Only re-renders when notifications change
const unread = useStore((s) => s.notifications.filter((n) => !n.read).length)

// ✅ Never re-renders (stable reference)
const updateUser = useStore((s) => s.updateUser)

// ✅ Shallow compare multiple values
import { shallow } from 'zustand/shallow'
const [name, theme] = useStore(
  (s) => ({ name: s.user.name, theme: s.theme }),
  shallow
)
```

Compare to signals where `.value` reads are auto-tracked — convenient but can be surprising when you read a signal inside a callback or an `effect()` and unintentionally subscribe to it.

#### 4. Middleware Ecosystem

Built-in middleware that works out of the box:

```typescript
import { create } from 'zustand'
import { persist, devtools, subscribeWithSelector } from 'zustand/middleware'
import { immer } from 'zustand/middleware/immer'

const useStore = create(
  devtools(
    persist(
      immer((set) => ({
        todos: [],
        addTodo: (text) =>
          set((state) => {
            state.todos.push({ id: Date.now(), text, done: false })
            // Immer allows "mutative" syntax
          }),
      })),
      { name: 'todos' }
    ),
    { name: 'TodoStore' }
  )
)
```

With signals, you'd need to wire up persistence, devtools, and immer manually.

#### 5. Predictable, Explicit Updates

State only changes when you call `set()` — no proxy magic, no hidden mutations:

```typescript
// Zustand: you see exactly where state changes
set((state) => ({ count: state.count + 1 }))

// Valtio: mutation-based (proxied)
state.count++ // fine, but harder to trace who mutated what

// Signals: .value assignment
count.value++ // also mutation-based at the signal level
```

This makes Zustand easier to debug, test, and reason about in large codebases.

---

### ❌ Cons

#### 1. Manual Selector Optimization (Footgun)

Get selectors wrong and you cause infinite re-renders or over-subscription:

```typescript
// ❌ BAD: New object every render → infinite loop
const { count, text } = useStore((state) => ({
  count: state.count,
  text: state.text,
}))

// ❌ BAD: Pulling entire store → re-renders on ANY change
const store = useStore()

// ❌ BAD: Destructuring the hook result
const { count, increment } = useStore()

// ✅ Fix: atomic selectors
const count = useStore((s) => s.count)
const text = useStore((s) => s.text)

// ✅ Fix: shallow comparison
import { shallow } from 'zustand/shallow'
const { count, text } = useStore(
  (s) => ({ count: s.count, text: s.text }),
  shallow
)
```

Signals avoid this entirely — auto-tracking guarantees you only re-run what reads the signal.

#### 2. No Built-in Derived State

Zustand has no `computed()` equivalent. You must DIY:

```typescript
// Zustand: manual derived state (two approaches)

// Approach A: Compute in selector (⚠️ expensive if called many times)
const useFilteredTodos = () => {
  const todos = useStore((s) => s.todos)
  const filter = useStore((s) => s.filter)
  return useMemo(() => todos.filter((t) => matches(t, filter)), [todos, filter])
}

// Approach B: Store computed values manually (⚠️ must keep in sync)
const useStore = create((set) => ({
  todos: [],
  filter: 'all',
  filteredTodos: [],
  setFilter: (filter) =>
    set((state) => ({
      filter,
      filteredTodos: state.todos.filter((t) => matches(t, filter)),
    })),
  addTodo: (text) =>
    set((state) => ({
      todos: [...state.todos, { id: Date.now(), text, done: false }],
      filteredTodos: [...state.todos, { id: Date.now(), text, done: false }]
        .filter((t) => matches(t, state.filter)),
    })),
}))

// Signals / Jotai: computed is built-in
const count = signal(0)
const doubled = computed(() => count.value * 2) // auto-derived, auto-cached
```

With Zustand's `subscribeWithSelector` middleware you can build your own reactivity, but it's not ergonomic.

#### 3. Multiple Stores Can Create Coordination Pain

Zustand encourages small stores, but cross-store logic requires manual wiring:

```typescript
// Two stores that need to coordinate
const useCartStore = create(() => ({ items: [], total: 0 }))
const useAuthStore = create(() => ({ user: null }))

// Action that depends on both stores
const checkout = () => {
  const user = useAuthStore.getState().user
  if (!user) throw new Error('Not logged in')

  const items = useCartStore.getState().items
  // ... submit order
}

// To subscribe to both changes:
useCartStore.subscribe(
  (items) => console.log('cart changed'),
  (s) => s.items
)
useAuthStore.subscribe(
  (user) => console.log('user changed'),
  (s) => s.user
)
```

With signals, you'd create a `computed(() => ...)` that reads from both — auto-tracking handles the cross-store dependency for free.

#### 4. Selectors Break with Destructuring / Rest

```typescript
// ❌ Destructuring subscribes to the full object
const { name, ...rest } = useStore((s) => s.user)
// rest contains everything in user — any user prop change re-renders

// ✅ Fix: be explicit
const name = useStore((s) => s.user.name)
const rest = useStore((s) => {
  const { name, ...rest } = s.user
  return rest
}, shallow)
```

This is tedious and error-prone. Signals don't have this problem — each `.value` read is independently tracked.

#### 5. No Built-in Async / Suspense Support

Zustand is synchronous. Async flows are manual:

```typescript
// Zustand: async action
const useStore = create((set) => ({
  data: null,
  loading: false,
  error: null,
  fetchData: async () => {
    set({ loading: true })
    try {
      const data = await api.get('/data')
      set({ data, loading: false })
    } catch (error) {
      set({ error, loading: false })
    }
  },
}))

// TanStack Query: handles caching, dedup, refetch, suspense, etc.
function useData() {
  return useQuery({
    queryKey: ['data'],
    queryFn: () => api.get('/data'),
  })
}
```

Zustand's docs explicitly recommend **TanStack Query for server state** and Zustand for client state. Signals don't solve this either — you'd still reach for TanStack Query or a similar tool.

#### 6. No Context-Like Scoping (Without Extra Work)

Zustand stores are global singletons. To get scoped instances (e.g., one store per form, per feature, per user session), you must use React Context:

```typescript
// Scoped Zustand store with Context
import { createContext, useContext } from 'react'
import { createStore, useStore } from 'zustand'

const FormContext = createContext(null)

const FormProvider = ({ children }) => {
  const storeRef = useRef(createStore((set) => ({
    values: {},
    errors: {},
    setField: (name, value) =>
      set((s) => ({ values: { ...s.values, [name]: value } })),
  })))

  return (
    <FormContext.Provider value={storeRef.current}>
      {children}
    </FormContext.Provider>
  )
}

const useFormStore = (selector) => {
  const store = useContext(FormContext)
  if (!store) throw new Error('Missing FormProvider')
  return useStore(store, selector)
}
```

With signals, you can scope them by declaring them in the right closure/module — no Context needed. But for React, you'd still need some mechanism to tie signal lifecycle to component lifecycle.

---

### Summary Table

| Concern | Zustand | Signals | Winner |
| --- | --- | --- | --- |
| Bundle size | ~1 KB | ~3 KB | Zustand |
| Re-render granularity | Component | Value/DOM | Signals |
| Learning curve | Low | Medium (Babel, auto-tracking) | Zustand |
| Outside React | Excellent | Good | Zustand |
| Middleware | Built-in | None | Zustand |
| DevTools | Redux DevTools | None | Zustand |
| Derived state | Manual | `computed()` built-in | Signals |
| Selector correctness | Manual (footgun) | Auto-tracked | Signals |
| Scoped/Context stores | Extra work | Natural | Signals |
| Async server state | Not its job | Not its job | Tie (use TanStack Query) |
| Ecosystem maturity | Battle-tested (5+ years) | Evolving | Zustand |
| TC39 future proof | Adapter possible | Language-native | Signals |

**Bottom line:** Zustand is the pragmatic choice today for most React apps — zero-config, tiny, works everywhere, and pairs perfectly with TanStack Query.
Signals offer a more elegant reactivity model but require Babel transforms and are still maturing in the React ecosystem. Once TC39 signals land natively and React adopts them, the landscape may shift — but that's years away.

## Resources

- [Official Documentation](https://zustand.docs.pmnd.rs/)
- [Flux-Inspired Practice Guide](https://zustand.docs.pmnd.rs/guides/flux-inspired-practice)
- [Zustand with Context API – An Advanced Pattern](https://www.youtube.com/watch?v=1Fi4hK7L1ec)
- [5 Zustand BEST Practices in 5 Minutes](https://www.youtube.com/watch?v=6tEQ1nJZ51w)
- [Zustand - Complete Tutorial](https://www.youtube.com/watch?v=_ngCLZ5Iz-0)
- [Working with Zustand (tkdodo)](https://tkdodo.eu/blog/working-with-zustand)
- [Zustand and React Context (tkdodo)](https://tkdodo.eu/blog/zustand-and-react-context)
- [Combining Zustand with React Query](https://www.youtube.com/watch?v=QTZTUrAbjeo)
