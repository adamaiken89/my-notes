# Frontend Architecture

## Thinking

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

https://github.com/enaqx/awesome-react

https://frontendatscale.com/courses/frontend-architecture/foundations/introduction/

https://github.com/alan2207/bulletproof-react?tab=readme-ov-file
