# Introduction to Software Design, Object Oriented Programming and Design Patterns

## Background

Software Design -> OOP -> Best Practices & Design Patterns

## Purpose of `software design`

-> A new requirement comes > change little / no change of system
-> The change of requirement is proportional to the amount of change of the system
-> Facilitate to add new codes instead of modifying the existing codes as much as possible

## Definition

- is a debatable concept
- two mainstream ways to describe OOP

## What is it with human wordings

1. A container (usually call `class`) that has `states` (non-accessible attributes) and a set of `behaviours` (methods) that can fetch / manipulate those states

2. An `object` must define the necessary states that `class` has specified before using the business rules in the `class`

3. Level of reuse
   - If existing class design satisfies the need, just use it
   - If only parts of it, can override some parts (i.e. `inheritance`) to create an extended `class`, then use it

4. Contracts - If the business rules behave with the same flow, to ensure it works similarly, static checking can be considered to apply to the class. It could be the input parameters (number of parameters) and its types, output parameter and its types and the methods (number of methods) and the name of the methods

## Technical terms

1. Four Pillars

   - Encapsulation - show only what you think are useful to outside world
   - Abstraction - group similar ideas together with INTERNAL states, outside world can interact with provided behaviours only (not states)
   - Inheritance - reuse existing functionality available without affecting existing business rules
   - Polymorphism -

2. Five Principles of Good OOP (SOLID)

   - Single-responsibility Principle
   - Open-closed Principle
   - Liskov Substitution Principle
   - Interface Segregation Principle
   - Dependency Inversion Principle

### P0 on OOP

- manage complexity
  - reduce unnecessary dependencies
  - separate infrastructure code from domain code

### P1 on OOP

- reuse business rules - write less by reuse more

### Think differently vs Functional Programming (FP)

- Level of reuse is DIFFERENT

- OOP
  - your unit is a CLASS, you want to reuse fully the existing CLASS or override some parts of it to partially reuse
  - the flow level design is often LEAN but procedural
  - you hide some implementations because it is rarely used, or the risk of change is high. Some parts become very easy to change - usually because the business wants to change it frequently, while data driven design would be overkilled (a lot of data structure defined, persistent storage, additional CRUD on such configurations)
- Functional
  - your unit is an independent function (pure functions), you want to reuse a lot of small explicit functions in procedural levels
  - A data flow is very explicit in flow level
  - The difficulty of change of each part is similar and linear

- OOP
  - Name methods as the usage because you need to encapsulate it
- Functional
  - Name methods as how it works because you want people to reuse it

## OOP (Java perspectives)

### Methods - When to have a new method

- for readability
  - break down a use case into small methods and a main method calls those
  - you can read main method easier

- for reusability
  - logics are so common and it reduces the work and errors repeating writing again and again
  - change of methods will automatically affect all the methods which use it

### Methods - When is private vs public

- always consider public static methods from another class but within the same package

### Methods - How to name a private method

- usage - it is just a part of the external method

### Methods - When to have a static method

- not that strict, do it when no internal dependency, some people prefer not

### Methods - When to have a public static method

- be avoided - in java context, it is more for factory object creation

- boundary can be unclear on classes with public static methods, especially with business rules

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

https://www.baeldung.com/java-static-methods-use-cases
