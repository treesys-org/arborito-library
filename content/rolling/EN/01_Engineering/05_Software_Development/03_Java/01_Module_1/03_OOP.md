@title: OOP in Java: classes and interfaces
@icon: 🏛️
@description: Abstract classes, interfaces, sealed types, composition.
@order: 3

# OOP in Java: classes and interfaces

Java provides **single inheritance** for classes (`extends`) and **multiple interface implementation** (`implements`). That avoids shared-state diamond problems while keeping polymorphism expressive.

@section: Classes

```java
public abstract class Shape {
    public abstract double area();
}

public class Circle extends Shape {
    private final double r;
    public Circle(double r) { this.r = r; }
    @Override public double area() { return Math.PI * r * r; }
}
```

Always mark overrides with `@Override`.

@section: Functional interfaces

Single abstract method interfaces pair with lambdas:

```java
@FunctionalInterface
public interface Predicate<T> { boolean test(T t); }
```

@section: sealed types

`sealed` restricts permitted subclasses—great for closed domain models.

@section: Composition over inheritance

Prefer **delegation** to deep class hierarchies that couple unrelated concerns.

@section: Pitfalls

* Inheritance for code reuse without an is-a relationship.
* Returning internal mutable collections.
* Broken `equals/hashCode` for `HashMap` keys.
@quiz: How many classes can a Java class extend directly?
@option: As many as you want
@correct: One
@option: Two at most
