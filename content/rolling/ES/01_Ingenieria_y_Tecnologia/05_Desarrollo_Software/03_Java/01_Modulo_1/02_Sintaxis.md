@title: Sintaxis Java esencial
@icon: 📝
@description: Tipos primitivos, var, records, texto y paquetes.
@order: 2

# Sintaxis Java esencial

Java mezcla **tipos primitivos** (`int`, `long`, `double`, `boolean`, `char`, …) con **tipos referencia** (objetos). Las variables de tipo referencia almacenan **direcciones** hacia objetos en el heap; los primitivos viven donde corresponda según el contexto (local, campo, etc.).

Desde Java 10, **`var`** infiere el tipo en **variables locales** cuando el compilador puede deducirlo sin ambigüedad. No es tipado dinámico: sigue siendo estático en tiempo de compilación.

```java
var lista = new ArrayList<String>(); // ArrayList<String>
final var PI = Math.PI;
```

@section: Texto: String y bloques

`String` es inmutable: cada “cambio” crea otro objeto. Para concatenaciones masivas usa `StringBuilder`.

```java
StringBuilder sb = new StringBuilder(128);
for (int i = 0; i < 1000; i++) sb.append(i).append(',');
String s = sb.toString();
```

Desde Java 15+, **text blocks** facilitan literales multilínea:

```java
String sql = """
    SELECT id, name
    FROM users
    WHERE active = true
    """;
```

@section: Records (Java 16)

Un **record** declara campos inmutables, `equals/hashCode/toString` y accesores automáticos — ideal para DTOs.

```java
public record Usuario(String id, String email) {}
```

@section: Paquetes y visibilidad

`package` organiza namespaces. Visibilidad: `private`, sin modificador (paquete), `protected`, `public`.

@section: Errores frecuentes

* Comparar strings con `==` en lugar de `Objects.equals` o `equals`.
* Usar `var` cuando el tipo inferido sea demasiado genérico y oculte errores.
* Olvidar que arrays son covariantes (`String[]` es subtipo de `Object[]`) — puede explotar en runtime.

@section: Laboratorio

1. Crea un `record Pedido(UUID id, BigDecimal total)` y úsalo en una `List`.
2. Mide `String +=` en bucle frente a `StringBuilder` con `System.nanoTime()`.
@quiz: ¿Qué palabra clave declara una variable local con inferencia de tipo?
@option: auto
@correct: var
@option: dynamic
