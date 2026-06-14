# Frontend Architecture

## Thinking

useState

- expose the state to be updated by external work  (setState)
- want to work with reactive chain action
  - business logic depends on the state (static reference)

useReducer

- state with multiple related action
- work amazingly with action & state discriminated union

useEffect

- browser component

useLayoutEffect

- position of component in the DOM to be avoid visual glitch

useRef

- reference to the DOM element
- state store without re-rendering
- use equal operator to assign value because it is mutable

## React

- very opinionated

not all the hooks are necessary to use

- useEffect is often a bad idea to most people
- there are quite a few hooks overlapping the use with tanstack query and zustand

- uncontrolled inputs vs controlled inputs

bulk data submission & data validation

- creating modal that can trigger everywhere

- preferences with explanation why prefer some tools over others
- esp. tools of tanstack query & zustand over some existing React Hooks

 zombie child problem, React concurrency, and context loss

interdependent states
aggregated actions across states in different stores

## LLM Assisted Engineering

- Use a blueprint-first structure: define types/contracts and a high-level component flow before implementation details.
- Keep sub-rendering helpers and custom hooks after the main component when practical to preserve top-down readability.

## Knowledge

Special properties children

```tsx
<parent>
  <children />
</parent>

// OR

<parent children={<children />} />


export const parent = ({ children }) => ...
```

## react internal queue to handle dispatch

dispatchSetState

- nested vs normal props

local states
-> state update -> components and its child components re-render

-> share states upwards => (useRef & useImperativeHandle)

-> share state downwards => one layer (pass by props)
=> a lot of layers (props drilling?)

```typescript
const CountContext = createContext([
    count: 0,
    setCount: () => {},
])

const ParenComponent = () => {
  const [count, setCount] = useState(1)

  return (<CountContext value={[count, setCount]}>

    <ChildComponent />
    <StaticComponent>
  </CountContext>)
}

//


export const ChildComponent = () => {
  const [count, setCount] = useContext(CountContext)

  return (<span>{count}</span>)
}

// no good, but consider using function call like logger or global service with Context
```

Context vs Zustand Store

Context can be overridden by nesting

## Opinions

### Keep the content of component exposed reasonably concise

- Keep functional components reasonably small (for example, around 40 LOC when practical).
- Consider sub-rendering and refactoring when rendering logic becomes hard to scan.
- The goal is clarity: you should always understand what a component renders and why.

## Features

- UI Component Framework
  - React

- State Management
  - React Hooks
  - Zustand
  - Tanstack Query

- Localization
  - i18next

- UI Component Library
  - Ant-design
  - AG-Grid

- Testing Library
  - Jest
  - Testing-library

- Bundler & Dev Server
  - vite

- Styles
  - JSS-in-CSS
  - SaSS
  - CSS modules
  - CSS4

- Static analyzer
  - Typescript
  - eslint
  - prettier

- Development Automation
  - Husky

## Approaches

- Features

- Maintainability
  - State Management
    - Application States
    - Component States
    - Remote Server Cache
  - Domain Management
  - Domain Separation & Arrangement

- Testing
  - Business Logic Focus
  - Component Focus
  - Business Focus

## Architecture

- Applications
  - introduces constraints when adding new things
  - props & states in components
  - when to use hooks
  - what should be in a single hook

## Preface

There are very little information about frontend architecture
No one cares but very important
as a product, good user experience, clean UI, clear instruction, fast response can make a good product. however, more engineers focus on server side to build than frontend.

building frontend is often painful, code quality is poor, modern frontend codebase is event-driven, need better and more effort to create automated test cases

## The Challenge of Frontend Engineering

- Un-controlled environment
  - Heavily depends on browsers
- More prone on non-engineering requirements
  - UI artists
- Fast iterations on frontend environments
  - e.g. create-react-app -> vite
  - e.g. react vs vue vs angular
  - hooks vs redux vs mobx vs zustand

- The unhealthy loop because of less good material and discussion -> less good engineers and materials

- Integrated nature by server interfaces and technology provided

- Faster AI revolution leads to AI-assisted programming style
  - define the system workflow
  - expand the technical and non technical requirement in a logical level
  - `vibe coding` / or review the AI generated work (workflow / implementation)

## Reference

<https://github.com/enaqx/awesome-react>

<https://frontendatscale.com/courses/frontend-architecture/foundations/introduction/>

<https://github.com/alan2207/bulletproof-react?tab=readme-ov-file>

<https://stackoverflow.com/questions/61280400/defining-a-sub-render-function-in-render-vs-a-react-component>
