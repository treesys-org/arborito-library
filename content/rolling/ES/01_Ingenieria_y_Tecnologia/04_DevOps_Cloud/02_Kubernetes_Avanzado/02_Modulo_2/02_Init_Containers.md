@title: Init containers: preparación antes del contenedor principal
@icon: 🪜
@description: Orden secuencial, reintentos y patrones de inicialización.
@order: 2

# Init containers: orden y precondiciones

Los **init containers** corren **antes** de los contenedores normales del pod, **en orden**, hasta completarse con éxito. Sirven para preparar volúmenes, esperar dependencias, **migrar esquemas**, o descargar certificados. Esta lección cubre semántica, recursos y límites prácticos.

@section: Semántica de ejecución

* Se ejecutan **secuencialmente** en el orden indicado.
* Si uno falla, Kubernetes reintenta según **restartPolicy** del pod.
* Todos deben completar **exit 0** antes de arrancar los contenedores normales.

**Red:** comparten el mismo network namespace **pod** (según versión y configuración de CNI; verifica comportamiento de `localhost` entre init y app).

@section: Casos de uso

* **Esperar** a que un servicio externo esté listo (`kubectl wait` pattern, `nc -z`).
* **Clonar** datos en un `emptyDir` compartido.
* **Ejecutar migraciones** antes de levantar la app (con cuidado en concurrencia).

**Antipatrón:** lógica de negocio larga en init: dificulta observabilidad y reintentos.

@section: Recursos y límites

Los init containers pueden tener **requests/limits** distintos; el **scheduler** considera el **máximo** entre init y app para ciertos recursos (según documentación de la versión).

**Imagen:** usa imágenes pequeñas y fijadas por digest en producción.

@section: Seguridad

* No montes credenciales amplias si solo necesitas lectura.
* **readOnlyRootFilesystem** cuando aplique.

@section: Comparación con hooks

**postStart** (hook del contenedor principal) es distinto: corre en paralelo a la app, no garantiza orden estricto ante readiness. Init es más adecuado para precondiciones.

@section: Errores frecuentes

* Init que nunca termina (dependencia caída) → pod bloqueado en `Init:0/1`.
* Asumir orden entre init y sidecars sin revisar la spec del pod (sidecars beta en versiones recientes).

@section: Laboratorio sugerido

1. Crea un pod con dos init containers que escriban en un volumen compartido y un contenedor principal que lea.
2. Simula fallo en el segundo init y observa reintentos.
3. Mide tiempo de arranque hasta `Ready`.

@quiz: ¿Cuándo se ejecutan los contenedores normales del pod respecto a los init containers?
@option: En paralelo desde el primer segundo
@correct: Solo después de que todos los init containers hayan terminado con éxito
@option: Solo si el init container está en la misma imagen
