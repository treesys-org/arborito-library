@title: Spring Boot: starters, autoconfiguración y perfiles
@icon: 🚀
@description: Convención sobre configuración, Actuator, propiedades externas.
@order: 2

# Spring Boot: starters, autoconfiguración y perfiles

**Spring Boot** empaqueta dependencias coherentes (**starters**) y **autoconfigura** beans comunes si detecta librerías en el classpath. Sigue siendo Spring: puedes sobreescribir todo con `@Configuration`.

Propiedades en `application.yml` o variables de entorno (`SERVER_PORT`, …). **Profiles** (`dev`, `prod`) separan configuración.

@section: Actuator

Expone endpoints operativos (`/actuator/health`, métricas) — protégelos en producción.

@section: Errores frecuentes

* Depender de `@Value` dispersos en lugar de `@ConfigurationProperties` tipado.
* Activar todo Actuator sin autenticación en internet.
@quiz: ¿Dónde suele vivir la configuración por defecto en un proyecto Boot?
@option: web.xml obligatorio
@correct: application.properties o application.yml
@option: solo variables sin fichero
