@title: Polimorfismo, enlace dinámico y patrones de diseño
@icon: 🔀
@description: Llamadas virtuales, double dispatch, instanceof moderno.
@order: 4

# Polimorfismo, enlace dinámico y patrones de diseño

Las llamadas a métodos de instancia en Java son **virtuales** por defecto: la JVM resuelve la implementación real según el **tipo runtime** del objeto, no el tipo de la variable que lo referencia.

```java
Figura f = new Circulo(2.0);
double a = f.area(); // llama a Circulo.area
```

@section: instanceof y pattern matching

```java
if (obj instanceof String s) {
    System.out.println(s.toLowerCase());
}
```

Evita casts ruidosos y `ClassCastException` cuando basta un guard.

@section: static vs dinámico

Los métodos `static` y los métodos `private` no participan en polimorfismo de subtipos igual que `public` virtuales.

@section: Errores frecuentes

* Usar sobrecarga pensando que “elige” la subclase más derivada (la sobrecarga se resuelve en **compilación**).
* Comparar con `getClass()` cuando bastaría un contrato de interfaz.
@quiz: ¿Qué anotación marca un método que sobrescribe otro?
@option: @Overload
@correct: @Override
@option: @Virtual
