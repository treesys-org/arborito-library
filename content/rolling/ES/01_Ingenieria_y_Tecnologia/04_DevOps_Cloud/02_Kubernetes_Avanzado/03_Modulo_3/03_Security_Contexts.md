@title: Security contexts: usuario, capabilities y seccomp
@icon: 🛡️
@description: Pod y container level, rootless y restricciones de privilegio.
@order: 3

# Security contexts: endurecer pods sin romper la app

**securityContext** permite fijar **usuario/grupo** (runAsUser), **capabilities**, **readOnlyRootFilesystem**, **seccomp** y **AppArmor/SELinux** (según nodo). Es la primera línea de defensa para **contener** un compromiso de contenedor.

@section: Nivel pod vs contenedor

* **pod.spec.securityContext:** aplica a todos los contenedores (fsGroup, supplementalGroups).
* **container.securityContext:** override por contenedor.

**runAsNonRoot:** falla el arranque si la imagen intenta root.

@section: Capabilities

Linux **capabilities** recortan privilegios de root. En Kubernetes puedes **drop** `ALL` y **add** solo las necesarias (`NET_BIND_SERVICE`, etc.).

**Imagen** debe soportar puertos >1024 o capabilities explícitas.

@section: seccomp y perfil

**seccomp** filtra syscalls. Puedes usar perfiles **RuntimeDefault** o **Localhost** (ruta a perfil). Probar en staging: perfiles estrictos rompen binarios maliciosamente compilados.

@section: readOnlyRootFilesystem

Monta **root** del contenedor como solo lectura; requiere **writes** a volumenes montados (`emptyDir`, tmpfs) para caches.

@section: allowPrivilegeEscalation

`allowPrivilegeEscalation: false` impide que procesos ganen más privilegios (p. ej. vía setuid).

@section: Pod Security Standards

**PSS** (baseline, restricted) integra políticas por namespace; **Pod Security Admission** en versiones recientes reemplaza a PSP en muchos casos.

@section: Errores frecuentes

* `runAsNonRoot` con imágenes que **exigen** root (UID 0).
* Drop de capabilities sin probar healthchecks que dependen de `ping`/`raw sockets`.

@section: Laboratorio sugerido

1. Crea un pod con `runAsUser: 1000` y verifica usuario dentro con `kubectl exec id`.
2. Habilita `readOnlyRootFilesystem` y añade `emptyDir` para `/tmp` si la app lo necesita.
3. Compara con **restricted** PSS en un namespace de prueba.

@quiz: ¿Qué hace `readOnlyRootFilesystem: true` en el securityContext del contenedor?
@option: Impide montar volúmenes
@correct: Evita escrituras en el filesystem raíz del contenedor salvo volúmenes montados para ello
@option: Desactiva TLS
