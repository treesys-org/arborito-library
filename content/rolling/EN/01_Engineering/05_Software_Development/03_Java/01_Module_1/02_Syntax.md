@title: Essential Java syntax
@icon: 📝
@description: Primitives, var, records, strings, packages.
@order: 2

# Essential Java syntax

Java mixes **primitive types** (`int`, `long`, `double`, `boolean`, `char`, …) with **reference types**. Reference variables hold **pointers** to heap objects; primitives behave like compact values.

Since Java 10, **`var`** infers the type for **local variables** when unambiguous — still **statically typed**.

```java
var list = new ArrayList<String>();
final var PI = Math.PI;
```

@section: Strings and text blocks

`String` is immutable. For heavy concatenation in loops, prefer `StringBuilder`.

```java
var sb = new StringBuilder(128);
for (int i = 0; i < 1000; i++) sb.append(i).append(',');
String s = sb.toString();
```

Java 15+ **text blocks**:

```java
String sql = """
    SELECT id, name
    FROM users
    WHERE active = true
    """;
```

@section: Records (Java 16)

A **record** defines immutable data carriers with generated `equals/hashCode/toString`.

```java
public record User(String id, String email) {}
```

@section: Packages and visibility

`package` namespaces code. Visibility: `private`, package-private, `protected`, `public`.

@section: Pitfalls

* Using `==` to compare `String` content.
* Overusing `var` where the inferred type is too generic.
* Array covariance surprises (`String[]` vs `Object[]`).

@section: Lab

1. Create `record Order(UUID id, BigDecimal total)` and store in a `List`.
2. Benchmark `String +=` vs `StringBuilder` in a loop.
@quiz: Which keyword declares a local variable with type inference?
@option: auto
@correct: var
@option: dynamic
