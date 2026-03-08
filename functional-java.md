# Functional Java

```java
public class Transaction {
  public void badWay() {
    startTransaction()
    // operations
    closeTransaction() // easy to be missed
  }

  // lambda expressions
  public void betterWay() {
    runWithTransaction((Transaction transaction) -> {
      // operations
    });
  }
}
```

## Modern Interface

- Function
- Predicate
- Consumer
- Supplier
- @FunctionalInterface

=> no more verbose anonymous inner class
=> less constructors
=> more lambda expressions

## map & apply

```java
prices.stream().map(price -> price * 0.9)

Stream<R> map(Function<T, R> mapper) {
  ...
  ... = mapper.apply(...);
  ...
}
```

## Collections

```java

// verbose
// ArrayList
friends.forEach(new Consumer<String>() {
  public void accept(final String name) {
    System.out.println(name);
  }
});

friends.forEach(System.out::println); // method reference

// stream API
friends
  .stream()
  .map(name -> name.toUpperCase()) // reference pipe
  .forEach(name -> System.out.println(name));

// back to array list
friends.stream().filter(name -> name.startsWith("N")).collect(Collectors.toList());

// array to stream
String[] names = {"Alice", "Bob", "Charlie"};
Arrays.stream(names).forEach(name -> System.out.println(name));

// java = lexical scope -> take key and bind to the later function reference
final Function<String, Predicate<String>> nameStartsWith = key -> name -> name.startsWith(key);

// partial application + invocation
// nameStartsWith.apply("N").test("Name") // result: true

// Common One

// map
// skip(Integer)
// dropWhile(expression)

// limit(Integer)
// takeWhile(expression)

// count() // aggregate
// collect(expression)
```
