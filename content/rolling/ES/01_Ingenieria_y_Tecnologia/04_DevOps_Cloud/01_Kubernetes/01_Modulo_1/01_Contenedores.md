
@title: Monolitos, microservicios y contenedores
@icon: 📦
@description: Por qué apareció la orquestación: del monolito al contenedor estándar OCI.
@order: 1

# De monolitos a contenedores

Durante décadas, muchas aplicaciones se desplegaban como **monolitos**: un único proceso (o pocos binarios) con lógica de negocio, persistencia y presentación acopladas. Eso simplifica el desarrollo inicial, pero **escalar** un cuello de botella concreto (solo la API, solo el front) obliga a escalar todo el paquete, y los despliegues son “todo o nada”.

@section: Microservicios

**Microservicio** no es un producto sino un **estilo arquitectónico**: muchos servicios pequeños, desplegables por separado, que se comunican por red (HTTP/gRPC/colas). Ventajas: equipos autónomos, escalado selectivo, fallos acotados si el diseño es bueno. Costes: **latencia** entre llamadas, **consistencia distribuida**, necesidad de **observabilidad** y de **contratos** (versionado de APIs) estrictos.

Kubernetes no “exige” microservicios: puedes ejecutar monolitos en Pods. Pero K8s brilla cuando hay **muchas unidades** que hay que programar, reiniciar y conectar.

@section: Qué es un contenedor

Un **contenedor** agrupa la aplicación y sus dependencias (librerías, ficheros) y comparte el **kernel** del sistema anfitrión con otros contenedores. No es una VM completa: es un proceso (o árbol de procesos) aislado mediante **namespaces** (vista de red, PID, montajes…) y **cgroups** (límites de CPU/memoria/I/O).

La **imagen** es un artefacto inmutable (capas de solo lectura) definida por un Dockerfile o equivalente; el **contenedor** es una instancia en ejecución de esa imagen más una capa escribible.

@section: Estándar OCI y runtime

**OCI** (Open Container Initiative) define formato de imagen y runtime. En la práctica verás **containerd** o **CRI-O** como runtime bajo Kubernetes; **Docker** sigue siendo herramienta de desarrollo y build de imágenes, aunque en el nodo el kubelet habla **CRI** con el runtime, no con el CLI de Docker.

@section: Por qué Kubernetes

Cuando tienes decenas o miles de contenedores en muchos nodos, hace falta: **programación** (qué va dónde), **recuperación** ante caídas, **servicio de red estable** hacia grupos de réplicas, **configuración y secretos**, **almacenamiento** y **actualizaciones** sin downtime. Eso es el rol del **plan de control** y los **workers**, tema de la siguiente lección.

@quiz: ¿Qué comparten los contenedores en el mismo host Linux?
@option: Un hipervisor tipo 1 y ROM de arranque común
@correct: El kernel del host y mecanismos como namespaces y cgroups
@option: Siempre un sistema de ficheros raíz idéntico bit a bit entre todos
