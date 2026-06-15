# Introduction to Software Design, Object Oriented Programming, and Design Patterns

## Background

Software Design -> OOP -> Best Practices & Design Patterns

## Purpose of `software design`

-> A new requirement comes > change little / no change of system
-> The change of requirement is proportional to the amount of change of the system
-> Facilitate to add new codes instead of modifying the existing codes as much as possible

## OOP Definition (Human Terms)

Debatable concept. Two mainstream descriptions:

1. Container (`class`) with `states` (private attributes) and `behaviours` (methods) that fetch/manipulate those states

2. `object` must define the necessary states that `class` has specified before using the business rules in the `class`

3. Levels of Reuse (from concrete to abstract)

   - **Level 0 (no reuse):** copy-paste. Highest duplication risk, no leverage.

   - **Level 1 — Implementation Reuse (business logic bound):**
        Unit of reuse is concrete class. Reuse code + behavior together.
        - Direct use: instantiate existing class as-is
        - Composition: delegate to existing class as member
        - Inheritance: extend class, override parts
        Low design effort, low flexibility. Business logic baked in.
        Relevant DPs: Adapter, Decorator, Composite.

   - **Level 2 — Contract Reuse (workflow reuse, business logic free):**
        Unit of reuse is interface / abstract class. Reuse structure and flow.
        - Define contract: method signatures, input/output types, method count
        - Implementations plug in with different business logic
        - Caller depends on contract, not concrete impl
        Higher design effort, high flexibility. Runtime polymorphic.
        Enables Dependency Inversion, Open-Closed Principle.
        Relevant DPs: Strategy, Template Method, Observer, Command.

   | Axis             | Level 1                  | Level 2                          |
   | ---------------- | ------------------------ | -------------------------------- |
   | Unit of reuse    | Concrete class           | Interface / abstract class       |
   | What you get     | Code + behavior bundled  | Structure / flow only            |
   | Binding          | Compile-time, concrete   | Runtime, polymorphic             |
   | Flexibility      | Low (logic locked)       | High (swap impl freely)          |
   | Change tolerance | Low — change propagates  | High — caller decoupled          |
   | Design effort    | Low — instant reuse      | Higher — upfront contract design |
   | Reduces          | Code volume (write less) | Coupling (change less)           |

4. Contracts
   - If business rules share same flow, static checking enforces consistent contract:
     input/output parameter types, method count and names
   - Compiler-enforced compliance (supports Level 2)

## Technical terms

1. Four Pillars

   - Encapsulation - show only what you think are useful to outside world
   - Abstraction - group similar ideas together with INTERNAL states, outside world can interact with provided behaviours only (not states)
   - Inheritance - reuse existing functionality available without affecting existing business rules
   - Polymorphism - same interface, different behaviour; child class can replace parent without changing caller (enables Level 2 contract reuse)

2. Five Principles of Good OOP (SOLID)

   - Single-responsibility Principle
   - Open-closed Principle
   - Liskov Substitution Principle
   - Interface Segregation Principle
   - Dependency Inversion Principle

### Principle 0 on OOP

- manage complexity
  - reduce unnecessary dependencies
  - separate infrastructure code from domain code

### Principle 1 on OOP

- reuse business rules - write less by reuse more

### OOP vs FP: Key Differences

| Axis              | OOP                                                                        | FP                                           |
| ----------------- | -------------------------------------------------------------------------- | -------------------------------------------- |
| Unit of reuse     | Class / Interface (Levels 1 & 2)                                           | Pure function                                |
| Change tolerance  | Uneven — isolate volatile parts behind interfaces, rigid cold paths hidden | Uniform — each change costs similar effort   |
| Flow visibility   | Encapsulated behind method calls — explicit at call site only              | Explicit data pipelines throughout           |
| Abstraction cost  | High upfront (class/contract design), low per change                       | Low upfront, repeats via composition         |
| Naming convention | Methods named by *usage/role* (what caller needs)                          | Functions named by *behavior* (what it does) |
| When to choose    | Change concentrated in specific areas                                      | Change evenly distributed across system      |

See [Java Guidelines](./java-guidelines.md) for Java-specific method conventions.

## Design Patterns

## Reference

- Software Design

  - (Article) [Design Stamina Hypothesis](https://martinfowler.com/bliki/DesignStaminaHypothesis.html)

- Object Oriented Programming

  - (Video) [Object Oriented Programming is not what I thought](https://www.youtube.com/watch?v=TbP2B1ijWr8)
  - (Video) [SOLID Object-Oriented Design](https://www.youtube.com/watch?v=v-2yFMzxqwU)
  - (Video) [Talk Session: Polly Want a Message](https://www.youtube.com/watch?v=YtROlyWWhV0)
  - (Book) [Clean Code](https://www.oreilly.com/library/view/clean-code-a/9780136083238)
  - (Book) [99 Bottles of OOP (Ruby Version)](https://sandimetz.com/99bottles), 2nd Edition
  - (Book) [Practical Object-Oriented Design, An Agile Primer Using Ruby, 2nd Edition](https://www.poodr.com)
  - (Book) [Simple Object-Oriented Design: Create Clean, Maintainable Applications](https://www.goodreads.com/book/show/211990834) by Mauricio Aniche (Goodreads: 4.3/5)
  - Simple Object Design
  - Object Design Style Guide

- Design Patterns

  - (Book) [Head First Design Patterns, 2nd Edition](https://www.oreilly.com/library/view/head-first-design/9781492077992)
  - (Book) [Design Patterns: Elements of Reusable Object-Oriented Software](https://www.oreilly.com/library/view/design-patterns-elements/0201633612)
  - (Website) [Refactoring Guru (Design Patterns)](https://refactoring.guru/design-patterns)

<https://www.baeldung.com/java-static-methods-use-cases>
