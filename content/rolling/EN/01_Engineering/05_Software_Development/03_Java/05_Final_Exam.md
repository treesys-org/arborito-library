
@title: Certification Exam: Java and the Spring ecosystem
@exam
@icon: ☕
@description: From bytecode to microservices: JVM, collections, Spring, and operations. Pass the majority to certify the track.
@order: 5

# Final exam: Java and Spring

Four blocks: language and JVM, advanced standard library, Spring, and production concerns.

> **Instructions:** Single best answer per question; a majority must be correct to pass.


## Block 1: JVM and language (module 1)

@quiz: Where does the JVM run Java bytecode?
@option: Directly on hardware with no intermediate layer.
@correct: On the virtual machine, which interprets or JIT-compiles depending on implementation.
@option: Only inside the `gcc` compiler.
@option: Only in the browser.

@quiz: Which keyword declares a local variable with type inference in Java 10+?
@option: `dynamic`
@correct: `var`
@option: `let`
@option: `auto`

@quiz: How many classes can a Java class extend directly?
@option: Unlimited via repeated `extends`.
@correct: Exactly one superclass (single inheritance of implementation).
@option: Two if one is abstract.
@option: Zero; Java has no inheritance.

@quiz: Which annotation marks a method intended to override a superclass method?
@option: `@Inherited`
@correct: `@Override`
@option: `@Implements`
@option: `@Replace`

@quiz: Which interface enables try-with-resources for safe cleanup?
@option: `Serializable`
@correct: `AutoCloseable`
@option: `Cloneable`
@option: `Readable`

@quiz: A `static` method on a class:
@option: Can access instance fields directly without an object.
@correct: Belongs to the class; there is no implicit instance `this`.
@option: Overrides instance methods the same way.
@option: Cannot be `public`.


## Block 2: Collections, streams, concurrency (module 2)

@quiz: Which `Map` keeps keys sorted by natural order or a `Comparator`?
@option: `HashMap`
@correct: `TreeMap`
@option: `WeakHashMap`
@option: `IdentityHashMap`

@quiz: In PECS, if a list only produces `T` elements, which wildcard fits producers?
@option: `List<? super T>`
@correct: `List<? extends T>`
@option: `List<T>` is always forbidden.
@option: `List<*>` with no bounds.

@quiz: Which kind of Stream operation actually executes the pipeline?
@option: An intermediate op like `map`.
@correct: A terminal op like `collect`, `forEach`, `reduce`.
@option: Only `parallel()`.
@option: `iterator()` alone.

@quiz: Which interface typically models a reusable thread pool?
@option: `Runnable` only.
@correct: `ExecutorService` (via `Executors` factories).
@option: `ThreadLocal`
@option: Only `ForkJoinTask`.

@quiz: Which modern API represents portable file paths?
@option: Only `java.io.File` since Java 8+.
@correct: `java.nio.file.Path` (and `Paths`).
@option: `URL` for every local file.
@option: Raw `String` with no API.

@quiz: A well-tuned `HashMap` offers average case:
@option: Always O(n) key lookup.
@correct: Expected O(1) lookup with a good hash and capacity.
@option: Lexicographically sorted keys.
@option: Only `String` keys.


## Block 3: Spring (module 3)

@quiz: Which injection style is preferred in modern Spring?
@option: Field injection with mandatory `@Autowired`.
@correct: Constructor injection (final dependencies in the ctor).
@option: Manual JNDI lookups only.
@option: Manual singletons with `new` per bean.

@quiz: Which annotation commonly marks a REST controller?
@option: Bare `@Controller`.
@correct: `@RestController` (`@Controller` + `@ResponseBody`).
@option: `@Servlet`
@option: JAX-WS `@Endpoint` only.

@quiz: Which interface do Spring Data JPA repositories usually extend?
@option: Only JDBC templates.
@correct: `JpaRepository` is the typical JPA starting point.
@option: Raw `EntityManager` only.
@option: No interface—only concrete DAOs.

@quiz: What does a CSRF token primarily mitigate for cookie-backed sessions?
@option: Static JSON XSS.
@correct: Cross-site forced state-changing requests.
@option: Server memory leaks.
@option: Unsafe blob deserialization.

@quiz: Where does Spring Boot conventionally keep primary configuration?
@option: Only OS environment variables.
@correct: `application.properties` / `application.yml` plus auto-configuration.
@option: Mandatory `web.xml`.
@option: Windows registry.

@quiz: `@SpringBootApplication` conceptually combines:
@option: Only `@Service`.
@correct: `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.
@option: Only `@Repository`.
@option: Only OAuth2 security.


## Block 4: Testing, microservices, operations (module 4)

@quiz: Which JUnit 5 annotation marks test methods?
@option: `@TestCase`
@correct: `@Test`
@option: Only JUnit 4 `@RunWith`.
@option: `@Check`

@quiz: When splitting a monolith into many microservices, what usually increases?
@option: Operational simplicity always.
@correct: Operational complexity (deployments, observability, distributed consistency).
@option: Monolith compile time only.
@option: Exclusive use of one shared relational DB.

@quiz: Which delivery guarantee often implies retries and possible duplicates?
@option: Strict at-most-once with no dupes.
@correct: At-least-once delivery.
@option: Free exactly-once everywhere.
@option: Fire-and-forget with mandatory ACK.

@quiz: Which file declares Maven coordinates and dependencies?
@option: `build.gradle`
@correct: `pom.xml` for Maven projects.
@option: `package.json`
@option: `Cargo.toml`

@quiz: Which Spring Boot starter adds operational endpoints like health?
@option: `spring-boot-starter-web` alone.
@correct: `spring-boot-starter-actuator`.
@option: `spring-boot-starter-data-jpa`
@option: `spring-boot-starter-mustache`

@quiz: In messaging, a *topic* compared with a classic queue often means:
@option: Exactly one consumer always.
@correct: Publish/subscribe with multiple possible subscribers (broker-dependent).
@option: Disk persistence is forbidden.
@option: Only synchronous HTTP calls.
