@title: JUnit 5, Mockito y pruebas de integración
@icon: 🧪
@description: Extensiones, testcontainers introductorio, asserts modernos.
@order: 1

# JUnit 5, Mockito y pruebas de integración

**JUnit Jupiter** usa `@Test` en `org.junit.jupiter.api`. **ParameterizedTest** cubre tablas de casos.

**Mockito** crea dobles: `when(repo.findById(id)).thenReturn(Optional.of(entity))`. Verifica interacciones con `verify`.

@section: Pirámide de tests

Muchas **unitarias**, menos **integración**, pocos **E2E**. Los tests lentos y frágiles deben ser minoría.
@quiz: ¿Qué API de JUnit 5 marca métodos de test?
@option: org.junit @Test legacy solo
@correct: org.junit.jupiter.api @Test
@option: @RunWith siempre
