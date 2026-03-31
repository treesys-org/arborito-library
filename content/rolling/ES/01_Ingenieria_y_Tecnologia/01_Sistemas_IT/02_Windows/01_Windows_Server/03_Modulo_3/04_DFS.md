@title: DFS: Namespaces y Replicación
@icon: 🌳
@description: Unificando y replicando carpetas compartidas a través de la red.
@order: 4

# DFS: espacios de nombres y replicación

**DFS (Distributed File System)** ofrece dos piezas: **DFS Namespaces** (un nombre UNC lógico que agrupa varias carpetas) y **DFS Replication** (DFS-R) para replicar contenido entre servidores (modelo multimaster con resolución de conflictos).

@section: Objetivos didácticos

*   Crear un **namespace** basado en dominio (`\\dominio\Datos`).
*   Añadir **carpetas** con **targets** en varios servidores.
*   Entender **replicación** y **topología** (hub/spoke).

@section: DFS Namespaces

*   **Namespace raíz:** punto de montaje lógico.
*   **Folder targets:** rutas físicas (`\\srv1\share`, `\\srv2\share`).

**Ventaja:** migras servidores sin cambiar letras de unidad en cliente: actualizas el `target`.

@section: Modos de acceso

*   **Referral:** el cliente recibe la lista ordenada de targets; **prioridad** y **orden** definen qué servidor usar.
*   **Client failback:** vuelve al servidor preferido cuando vuelve a estar online.

@section: DFS Replication

Requisitos:

*   **AD** y **replicación de AD** funcionando.
*   **Topología** definida (full mesh o hub).
*   **Horario** y **ancho de banda** para no saturar WAN.

**No uses DFS-R** como reemplazo de backup: es **replicación**, no sustituto de snapshots ni copias offline.

@section: Monitoreo

*   Consola **DFS Management**.
*   **Eventos** de DFS Replication en visor.
*   `dfsrdiag` en diagnóstico avanzado.

@section: Buenas prácticas

*   Dimensiona **staging** y **conflictos** (carpeta `DfsrPrivate`).
*   Evita **replicar** bases de datos abiertas (SQL, VHDX montados) sin aplicación compatible.

@section: Práctica

1.  Crea un namespace de dominio y dos targets en carpetas distintas.
2.  Simula caída de un servidor y observa el **failover** (tiempo de detección).

@quiz: ¿Qué componente de DFS proporciona un nombre UNC único que agrupa varias carpetas físicas?
@option: DFS Replication
@correct: DFS Namespaces
@option: WSUS

@quiz: ¿Qué advertencia es correcta sobre DFS Replication?
@option: Sustituye por completo a un sistema de backup en cinta
@correct: Replica archivos entre servidores pero no reemplaza la estrategia de backup empresarial
@option: Solo funciona en Windows 7
