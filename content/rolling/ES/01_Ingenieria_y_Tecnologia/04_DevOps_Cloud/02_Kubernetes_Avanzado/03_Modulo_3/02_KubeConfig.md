@title: kubeconfig: contextos, clusters y credenciales
@icon: ⚙️
@description: Múltiples entornos, exec plugins y buenas prácticas de seguridad.
@order: 2

# kubeconfig: gestionar acceso sin perder la cabeza

El archivo **kubeconfig** (por defecto `~/.kube/config`) describe **clusters**, **usuarios** (credenciales) y **contextos** que unen ambos con un **namespace** por defecto. Dominar **merge** de configs, **exec plugins** y **minimización de permisos** evita incidentes «apliqué a producción creyendo que era dev».

@section: Estructura

* **clusters:** endpoint del apiserver + CA.
* **users:** cert/key, token, o `exec` para obtener token dinámico (OIDC, cloud).
* **contexts:** cluster + user + `namespace` default.
* **current-context:** el que usa `kubectl` sin flags extra.

@section: Múltiples entornos

Usa **nombres explícitos** (`prod-admin`, `dev-readonly`). **Alias** shell (`kdev`, `kprod`) reducen errores.

**KUBECONFIG** puede listar varios archivos separados por `:`; kubectl los fusiona.

@section: Exec plugins

Proveedores cloud y OIDC suelen usar **exec plugins** que obtienen tokens **cortos**. **No** guardes secretos largos en texto plano si hay alternativa.

**Caducidad:** tokens expiran; CI debe refrescar credenciales.

@section: RBAC y principio de mínimo privilegio

Incluso con kubeconfig correcto, **Roles/ClusterRoles** limitan lo que puedes hacer. Para CI, **ServiceAccounts** dedicadas con permisos mínimos.

@section: Impersonación

`kubectl --as` y `--as-group` permiten probar políticas; **no** abuses en producción sin auditoría.

@section: Errores frecuentes

* Mismo **context** apuntando a distintos clusters tras copiar configs.
* `insecure-skip-tls-verify: true` en producción.
* Compartir kubeconfig de admin por chat.

@section: Laboratorio sugerido

1. Crea dos contextos de prueba (o usa minikube + kind) y cambia con `kubectl config use-context`.
2. Exporta manifests con `kubectl` usando `--context` explícito.
3. Configura un alias que muestre el contexto actual en el prompt (opcional).

@quiz: ¿Qué une un "context" en kubeconfig?
@option: Solo el namespace
@correct: Un cluster, un usuario de autenticación y opcionalmente el namespace por defecto
@option: Solo el certificado TLS
