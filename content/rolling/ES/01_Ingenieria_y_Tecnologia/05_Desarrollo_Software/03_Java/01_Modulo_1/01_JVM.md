@title: JVM, bytecode y recolección de basura
@icon: ☕
@description: HotSpot, JIT, heap, metaspace, GC y pausas.
@order: 1

# JVM, bytecode y recolección de basura

Cuando compilas con `javac`, no obtienes código máquina directamente: obtienes **bytecode** almacenado en ficheros `.class`. La **máquina virtual Java (JVM)** es el intérprete y motor de optimización que ejecuta ese bytecode en Linux, Windows o macOS sin recompilar el proyecto entero para cada sistema operativo.

Esta lección fija el mapa mental: **JDK vs JRE vs JVM**, cómo se carga una clase, dónde viven los objetos y por qué a veces la aplicación “se para” unos milisegundos (**GC pause**).

@section: JDK, JRE y JVM

* **JDK (Java Development Kit):** incluye `javac`, herramientas (`jcmd`, `jmap`, `jfr`), y normalmente también un **JRE** empaquetado.
* **JRE (Java Runtime Environment):** JVM + bibliotecas estándar para **ejecutar** (sin compilar).
* **JVM:** motor de ejecución: intérprete + compiladores JIT + subsistema de memoria y GC.

En servidores modernos casi siempre instalas un **JDK** aunque solo vayas a ejecutar, porque las herramientas de diagnóstico vienen con él.

@section: Bytecode y ficheros .class

El bytecode es un lenguaje intermedio **apilado** (stack-based): las instrucciones manipulan una pila de operandos. Puedes inspeccionar con:

```bash
javap -c -p mi/paquete/MiClase
```

Verás mnemónicos como `aload`, `invokevirtual`, `return`. No necesitas memorizarlos, pero sí entender que **cada método** se traduce a una secuencia de instrucciones verificables por la JVM antes de ejecutarse (**bytecode verifier**).

@section: Carga de clases y ClassLoader

Las clases se cargan **bajo demanda**. Los **ClassLoader** jerárquicos (bootstrap, platform, application) buscan recursos en el **classpath** o en el **module-path** (Java 9+). Errores típicos:

* `ClassNotFoundException` — el nombre existe en el código fuente pero el `.class` no está en el classpath en tiempo de ejecución.
* `NoClassDefFoundError` — la clase estuvo disponible al compilar pero falló al cargar en runtime (dependencia faltante).

@section: Memoria: heap, metaspace y pilas

* **Heap:** objetos Java (instancias). Ahí actúa el **recolector de basura**.
* **Metaspace** (sustituto moderno de PermGen): metadatos de clases, métodos JIT, etc.
* **Stack por hilo:** marcos de pila con variables locales y referencias; **no** es donde viven los objetos grandes.

@section: JIT: de intérprete a código nativo

La JVM arranca interpretando bytecode. Los métodos **calientes** se recompilan con **JIT** (HotSpot tiene compiladores **C1** “rápido” y **C2** “optimizador profundo”). Por eso las benchmarks deben **calentar** antes de medir: la primera iteración puede ser mucho más lenta.

@section: Recolectores y pausas (G1, ZGC, Shenandoah)

No hay “un solo GC perfecto”. **G1** divide el heap en regiones y intenta acotar pausas. **ZGC** y **Shenandoah** apuntan a pausas muy bajas en heaps grandes, con trade-offs de CPU y complejidad operativa.

Tabla orientativa (no reglas absolutas):

| Síntoma observado | Posible lectura |
|-------------------|-----------------|
| Pausas largas puntuales | GC stop-the-world, tuning o bug de asignación |
| CPU alto constante | GC luchando con demasiados objetos vivos o fugas |
| Latencias estables pero RSS alto | heap grande reservado |

@section: JPMS (módulos) en una frase

Desde Java 9, `module-info.java` declara qué paquetes se **exportan** y qué módulos se **requieren**. Sustituye parte del caos del “classpath plano” en aplicaciones grandes.

@section: Laboratorio guiado

1. Compila un `Hello.java` y observa el `.class` generado.
2. Ejecuta con `java -Xlog:gc*` (según versión) y mira cómo cambia el log al crear muchos objetos temporales en un bucle.
3. Usa `jcmd <pid> VM.flags` para ver flags efectivos.

@section: Errores frecuentes

* Medir rendimiento sin **warmup** JIT.
* Confundir **referencia null** con objeto recolectado mientras otro hilo lo usa (**race**).
* Ajustar flags de GC sin métricas (**GC logs**, **JFR**).
@quiz: ¿Qué componente convierte métodos bytecode calientes en código nativo?
@option: Solo el enlazador estático
@correct: Compilador JIT
@option: javac en cada ejecución
