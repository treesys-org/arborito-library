
@title: Examen de certificación: Fundamentos de computación
@exam
@icon: 🧮
@description: Procesos, hardware lógico, estructuras de datos y algoritmos. Aprueba la mayoría para certificar el curso.
@order: 5

# Examen final: Fundamentos de computación

Cuatro bloques alineados con los módulos: sistema y memoria, CPU y lógica, Estructuras y complejidad, algoritmos clásicos y compiladores.

> **Instrucciones:** Una respuesta válida por pregunta; mayoría para aprobar.


## Bloque 1: Procesos, concurrencia y E/S (módulo 1)

@quiz: ¿Qué estructura mantiene el kernel con metadatos de un proceso?
@option: Solo la tabla de páginas.
@correct: PCB / descriptor de proceso (process control block).
@option: El buffer del disco únicamente.
@option: La cola de interrupciones del usuario.

@quiz: ¿Qué condición define típicamente un *deadlock* entre hilos?
@option: Un hilo prioriza siempre a otro sin espera.
@correct: Espera circular sosteniendo recursos mientras se piden otros (condiciones de Coffman).
@option: Un único hilo en ejecución.
@option: Uso de colas lock-free.

@quiz: ¿Para qué sirve principalmente la TLB?
@option: Traducir nombres DNS.
@correct: Cachear traducciones de direcciones virtuales a físicas (MMU).
@option: Bufferizar paquetes de red.
@option: Almacenar el árbol de procesos.

@quiz: Un i-nodo en sistemas tipo Unix almacena típicamente:
@option: El nombre visible del archivo en directorio.
@correct: Metadatos del archivo (permisos, tamaño, punteros a bloques); el nombre está en la entrada de directorio.
@option: Solo el contenido del archivo inline.
@option: La contraseña del propietario.

@quiz: ¿Qué mecanismo permite a dispositivos transferir datos a memoria sin ocupar la CPU en cada byte?
@option: Busy waiting en un bucle cerrado.
@correct: DMA (acceso directo a memoria).
@option: Solo interrupciones software.
@option: Paginación pura sin hardware.

@quiz: La sección crítica en programación concurrente es:
@option: Todo el programa multihilo.
@correct: El fragmento que accede a recursos compartidos y debe protegerse con sincronización.
@option: Solo código que no usa mutex.
@option: El espacio de usuario completo.


## Bloque 2: Lógica, ISA y rendimiento (módulo 2)

@quiz: ¿Qué ley booleana relaciona NOT(A AND B) con (NOT A) OR (NOT B)?
@option: Ley de absorción.
@correct: Ley de De Morgan.
@option: Ley conmutativa exclusiva.
@option: Ley de idempotencia doble.

@quiz: ¿Qué describe principalmente una ISA?
@option: El sistema operativo concreto instalado.
@correct: El contrato instrucciones/registros/modos de direccionamiento visibles al software de bajo nivel.
@option: Solo el planificador del SO.
@option: El formato de ficheros PDF.

@quiz: ¿Qué tipo de hazard de pipeline ocurre si una instrucción necesita un registro aún no escrito por la anterior?
@option: Hazard estructural.
@correct: Hazard de datos (RAW típico).
@option: Hazard de control únicamente.
@option: Hazard de caché L3.

@quiz: ¿Qué problema intenta resolver la coherencia de caché en CPUs multinúcleo?
@option: Falta de RAM física.
@correct: Copias inconsistentes de la misma línea de memoria en distintas cachés.
@option: Traducción DNS.
@option: Planificación de disco magnético.

@quiz: ¿Qué ley limita la aceleración si una fracción del programa es inherentemente secuencial?
@option: Ley de Moore.
@correct: Ley de Amdahl.
@option: Ley de Little exclusivamente de redes.
@option: Principio de Pareto puro.

@quiz: Un sumador completo difiere de un medio sumador porque:
@option: Solo opera con números hexadecimales.
@correct: Incorpora acarreo de entrada además de generar acarreo de salida.
@option: No produce acarreo.
@option: Requiere reloj de GPU.


## Bloque 3: Complejidad y estructuras (módulo 3)

@quiz: ¿Qué representa típicamente la notación O(...) en algoritmos?
@option: Tiempo exacto en segundos en una CPU dada.
@correct: Crecimiento asintótico del coste frente al tamaño de entrada (cota superior habitual).
@option: Uso exacto de bytes en heap.
@option: Número de líneas de código.

@quiz: ¿Qué operación es típicamente O(1) en un arreglo por índice válido?
@option: Buscar un valor desconocido sin orden previo.
@correct: Acceso indexado al elemento.
@option: Insertar al inicio desplazando todo.
@option: Eliminar el mínimo en un heap arbitrario sin más datos.

@quiz: ¿Qué recorrido de un BST válido visita las claves en orden ascendente?
@option: Preorden.
@correct: Inorden.
@option: Postorden exclusivamente.
@option: Nivel orden aleatorio.

@quiz: ¿Qué fenómeno aumenta *probes* y degrada rendimiento si el factor de carga de una tabla hash abierta es alto?
@option: Árbol balanceado automático.
@correct: Colisiones y clustering de ranuras.
@option: Uso de listas enlazadas vacías.
@option: Compresión LZ4.

@quiz: ¿Qué recorrido de grafos usa típicamente una cola para visitar por capas?
@option: DFS con pila explícita.
@correct: BFS.
@option: Dijkstra sin pesos.
@option: Topological sort obligatorio.

@quiz: Un grafo dirigido acíclico (DAG) es prerequisito natural para:
@option: Detectar ciclos con multiplicidad infinita siempre.
@correct: Ordenamiento topológico de tareas dependientes.
@option: Colorear siempre con 2 colores.
@option: Árbol de expansión mínima sin pesos.


## Bloque 4: Algoritmos y compiladores (módulo 4)

@quiz: ¿Qué propiedad conserva un orden estable ante claves iguales?
@option: Complejidad O(1) garantizada.
@correct: El orden relativo original de esos registros.
@option: Uso mínimo de memoria externa.
@option: Que sea in-place siempre.

@quiz: ¿Qué requisito esencial exige la búsqueda binaria clásica sobre un arreglo?
@option: Datos almacenados en lista enlazada.
@correct: Secuencia ordenada por la clave buscada (con acceso aleatorio O(1) al índice).
@option: Solo números primos.
@option: Que el arreglo sea de tamaño par.

@quiz: ¿Qué condición hace útil la programación dinámica frente a divide y vencerás ingenuo?
@option: Sin subproblemas compartidos.
@correct: Subproblemas superpuestos y subestructura óptima.
@option: Solo grafos cíclicos densos.
@option: Entrada siempre ordenada.

@quiz: ¿Qué se requiere típicamente para demostrar corrección de un algoritmo *greedy*?
@option: Solo que sea rápido en benchmarks.
@correct: Propiedad de elección greedy y subestructura óptima (o argumento de intercambio).
@option: Que use backtracking.
@option: Memoria O(n²) mínima.

@quiz: ¿Qué estructura representa típicamente la sintaxis abstracta del programa en un compilador?
@option: Código máquina final.
@correct: AST (árbol de sintaxis abstracta).
@option: Solo tabla de literales.
@option: Bitmap de fuentes.

@quiz: La fase de análisis léxico produce principalmente:
@option: Código objeto enlazado.
@correct: Tokens a partir del flujo de caracteres fuente.
@option: Plan de optimización de bucles únicamente.
@option: Diagramas de despliegue.
