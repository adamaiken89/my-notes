# Java Guidelines

## Methods - When to have a new method

- for readability
  - break down a use case into small methods and a main method calls those
  - you can read main method easier

- for reusability
  - logics are so common and it reduces the work and errors repeating writing again and again
  - change of methods will automatically affect all the methods which use it

### Methods - When is private vs public

- always consider public static methods from another class but within the same package

### Methods - How to name a private method

- usage - it is a part of the external method

### Methods - When to have a static method

- not that strict, do it when no internal dependency, some people prefer not

### Methods - When to have a public static method

- be avoided - in java context, it is more for factory object creation

- boundary can be unclear on classes with public static methods, especially with business rules
