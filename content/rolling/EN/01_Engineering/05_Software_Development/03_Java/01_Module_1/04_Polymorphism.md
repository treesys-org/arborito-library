@title: Polymorphism and dynamic dispatch
@icon: 🔀
@description: Virtual calls, overload vs override, pattern matching.
@order: 4

# Polymorphism and dynamic dispatch

Instance method calls are **virtual** by default: the JVM dispatches to the runtime type’s implementation.

```java
Shape s = new Circle(2.0);
double a = s.area(); // Circle.area
```

@section: pattern matching for instanceof

```java
if (obj instanceof String str) {
    System.out.println(str.toLowerCase());
}
```

@section: static vs dynamic

`static` and `private` methods are not polymorphic in the same way as public instance methods.

@section: Pitfalls

* Expecting overload resolution to pick the “most derived” runtime type — overloads bind at **compile time**.
* Overusing `getClass()` checks instead of interfaces.
@quiz: Which annotation marks a method that overrides a superclass method?
@option: @Overload
@correct: @Override
@option: @Virtual
