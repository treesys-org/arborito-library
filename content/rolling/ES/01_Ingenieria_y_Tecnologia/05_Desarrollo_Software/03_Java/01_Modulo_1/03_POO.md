@title: POO en Java: clases, interfaces y herencia
@icon: 🏛️
@description: Clases abstractas, interfaces, sealed, composición frente a herencia.
@order: 3

# POO en Java: clases, interfaces y herencia

Java ofrece **herencia simple** de clases (`extends`) e **implementación múltiple** de interfaces (`implements`). Una clase puede extender solo una superclase, pero implementar varias interfaces — esto evita el diamante de herencia múltiple a nivel de estado compartido.

@section: Clases y métodos

```java
public abstract class Figura {
    public abstract double area();
}

public class Circulo extends Figura {
    private final double r;
    public Circulo(double r) { this.r = r; }
    @Override public double area() { return Math.PI * r * r; }
}
```

Usa `@Override` para detectar errores de firma al refactorizar.

@section: Interfaces funcionales

Una interfaz con un solo método abstracto es **funcional** y puede usarse con lambdas:

```java
@FunctionalInterface
public interface Predicado<T> { boolean test(T t); }
```

@section: sealed (Java 17)

Limita qué clases pueden extender una jerarquía — útil para modelar variantes cerradas y `switch` exhaustivos.

@section: Composición

Prefiere **composición** (delegar en otros objetos) frente a jerarquías profundas que acoplan capas.

@section: Errores frecuentes

* Heredar para reutilizar código no relacionado semánticamente.
* Exponer colecciones mutables sin defensa (`return list` interna).
* Olvidar `equals/hashCode` en claves de `HashMap`.
@quiz: ¿Cuántas clases puede extender directamente una clase Java?
@option: Todas las que quieras
@correct: Una
@option: Dos como máximo
