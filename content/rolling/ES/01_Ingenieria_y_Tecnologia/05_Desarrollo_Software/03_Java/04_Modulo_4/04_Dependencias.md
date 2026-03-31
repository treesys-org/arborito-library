@title: Maven y Gradle: dependencias, BOM y conflictos
@icon: 📦
@description: Scopes, exclusiones, alineación de versiones.
@order: 4

# Maven y Gradle: dependencias, BOM y conflictos

**Maven** declara artefactos en `pom.xml` con **coordenadas** GAV. El **árbol de dependencias** puede traer versiones conflictivas: usa `dependencyManagement` o **BOMs** (p. ej. Spring Boot) para alinear.

```xml
<dependency>
  <groupId>org.example</groupId>
  <artifactId>lib</artifactId>
  <version>1.2.3</version>
</dependency>
```

@section: Scopes

`compile` (default), `runtime`, `test`, `provided` (contenedor aporta API).
@quiz: ¿Qué fichero declara un proyecto Maven?
@option: build.gradle.kts siempre
@correct: pom.xml
@option: package.json
