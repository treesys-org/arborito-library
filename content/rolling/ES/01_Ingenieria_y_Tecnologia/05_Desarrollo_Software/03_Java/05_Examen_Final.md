
@title: Examen de certificación: Java y ecosistema Spring
@exam
@icon: ☕
@description: Del bytecode al microservicio: JVM, colecciones, Spring y despliegue. Aprueba la mayoría para certificar el curso.
@order: 5

# Examen final: Java y ecosistema Spring

Evalúa cuatro bloques: lenguaje y JVM, librería estándar avanzada, Spring y producción.

> **Instrucciones:** Una respuesta correcta por pregunta; se requiere mayoría de aciertos.


## Bloque 1: JVM y lenguaje (módulo 1)

@quiz: ¿Dónde ejecuta la JVM el bytecode de Java?
@option: Directamente en el hardware sin capa intermedia.
@correct: En la máquina virtual, que interpreta o compila JIT según implementación.
@option: Solo en el compilador nativo `gcc`.
@option: En el navegador exclusivamente.

@quiz: ¿Qué palabra clave declara una variable local con inferencia de tipo en Java 10+?
@option: `dynamic`
@correct: `var`
@option: `let`
@option: `auto`

@quiz: ¿Cuántas clases puede extender directamente una clase Java?
@option: Ilimitadas con `extends` repetido.
@correct: Una única superclase (herencia simple de clase).
@option: Dos si una es abstracta.
@option: Cero; Java no tiene herencia.

@quiz: ¿Qué anotación indica que un método pretende sobrescribir un método de una superclase?
@option: `@Inherited`
@correct: `@Override`
@option: `@Implements`
@option: `@Replace`

@quiz: ¿Qué interfaz permite usar un recurso en try-with-resources de forma segura?
@option: `Serializable`
@correct: `AutoCloseable`
@option: `Cloneable`
@option: `Readable`

@quiz: Un método `static` en una clase:
@option: Puede acceder directamente a campos de instancia sin objeto.
@correct: Pertenece a la clase; no recibe `this` implícito de instancia.
@option: Se sobrescribe igual que un método de instancia.
@option: No puede ser `public`.


## Bloque 2: Colecciones, streams y concurrencia (módulo 2)

@quiz: ¿Qué `Map` mantiene las claves ordenadas según orden natural o `Comparator`?
@option: `HashMap`
@correct: `TreeMap`
@option: `WeakHashMap`
@option: `IdentityHashMap`

@quiz: En PECS, si una lista solo produce elementos `T`, ¿qué comodín usarías en la firma?
@option: `List<? super T>`
@correct: `List<? extends T>` para productores.
@option: `List<T>` siempre prohibido.
@option: `List<*>` genérico sin restricción.

@quiz: ¿Qué tipo de operación de Stream dispara realmente el pipeline?
@option: Operación intermedia como `map`.
@correct: Operación terminal como `collect`, `forEach`, `reduce`.
@option: `parallel()` solo.
@option: `iterator()` sin más.

@quiz: ¿Qué interfaz representa típicamente un pool de hilos reutilizable?
@option: `Runnable` solo.
@correct: `ExecutorService` (familia `Executors`).
@option: `ThreadLocal`
@option: `ForkJoinTask` únicamente.

@quiz: ¿Qué clase moderna representa rutas de fichero portables?
@option: `java.io.File` exclusivamente desde Java 8+.
@correct: `java.nio.file.Path` (y `Paths`).
@option: `URL` para todo fichero local.
@option: `String` sin API adicional.

@quiz: Un `HashMap` ofrece en promedio:
@option: Acceso por clave O(n) siempre.
@correct: Acceso esperado O(1) si el hash y la capacidad son adecuados.
@option: Claves ordenadas lexicográficamente.
@option: Solo claves `String`.


## Bloque 3: Spring (módulo 3)

@quiz: ¿Qué estilo de inyección de dependencias se recomienda en Spring moderno?
@option: Solo inyección por campo con `@Autowired` obligatorio.
@correct: Constructor injection (constructor con dependencias finales).
@option: Solo lookup JNDI manual.
@option: Singleton manual con `new` en cada bean.

@quiz: ¿Qué anotación marca típicamente un controlador REST?
@option: `@Controller` sin más.
@correct: `@RestController` (combinación de `@Controller` + `@ResponseBody`).
@option: `@Servlet`
@option: `@Endpoint` de JAX-WS únicamente.

@quiz: ¿Qué interfaz suelen extender los repositorios JPA en Spring Data?
@option: `JdbcTemplate` como interfaz de repositorio.
@correct: `JpaRepository` (patrón habitual para repositorios JPA).
@option: `EntityManager` directo sin interfaz de Spring Data.
@option: `HttpServletRequest`.

@quiz: ¿Qué riesgo mitiga un token CSRF en formularios con sesión?
@option: XSS en JSON estático.
@correct: Peticiones de cambio de estado forzadas desde otro sitio (cross-site).
@option: Fuga de memoria en el servidor.
@option: Deserialización insegura de blobs.

@quiz: En Spring Boot, ¿dónde suele vivir la configuración por convención principal?
@option: Solo variables de entorno del SO.
@correct: `application.properties` / `application.yml` y auto-configuración.
@option: `web.xml` obligatorio.
@option: Registro de Windows.

@quiz: `@SpringBootApplication` agrupa conceptualmente:
@option: Solo `@Service`.
@correct: `@Configuration`, `@EnableAutoConfiguration` y `@ComponentScan` (equivalente práctico).
@option: Solo `@Repository`.
@option: Solo seguridad OAuth2.


## Bloque 4: Testing, microservicios y operaciones (módulo 4)

@quiz: ¿Qué API de JUnit 5 anota métodos de test?
@option: `@TestCase`
@correct: `@Test`
@option: `@RunWith` solo (JUnit 4 exclusivo).
@option: `@Check`

@quiz: Al partir un monolito en muchos microservicios, ¿qué suele aumentar?
@option: Simplicidad operativa siempre.
@correct: Complejidad operativa (despliegues, observabilidad, consistencia distribuida).
@option: Tiempo de compilación del monolito original.
@option: Uso exclusivo de bases relacionales únicas.

@quiz: ¿Qué garantía de entrega suele implicar reintentos y posibles duplicados?
@option: At-most-once estricto sin duplicados.
@correct: At-least-once (puede repetir mensajes).
@option: Exactly-once siempre sin coste.
@option: Fire-and-forget con ACK obligatorio.

@quiz: ¿Qué fichero declara coordenadas Maven de artefactos y dependencias?
@option: `build.gradle`
@correct: `pom.xml` en proyectos Maven.
@option: `package.json`
@option: `Cargo.toml`

@quiz: ¿Qué starter de Spring Boot añade endpoints operativos como health?
@option: `spring-boot-starter-web` solo.
@correct: `spring-boot-starter-actuator` (habitual para health/info).
@option: `spring-boot-starter-data-jpa`
@option: `spring-boot-starter-mustache`

@quiz: En mensajería, un *topic* frente a una *cola* clásica suele implicar:
@option: Un solo consumidor obligatorio siempre.
@correct: Modelo pub/sub con múltiples suscriptores posibles (según broker).
@option: Persistencia en disco prohibida.
@option: Solo llamadas síncronas HTTP.
