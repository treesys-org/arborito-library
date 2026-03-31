
@title: ConfigMaps y Secrets
@icon: 🔐
@description: Configuración no secreta, datos sensibles, montaje y buenas prácticas.
@order: 4

# Configuración y secretos

**ConfigMap** almacena datos **no secretos** (flags, ficheros de config). **Secret** almacena datos sensibles; en etcd van **cifrados en reposo** si activas encryption at rest, pero en el nodo siguen siendo ficheros o variables—**no sustituyen** un vault empresarial.

@section: Inyección en Pods

*   **env** / **envFrom** para variables.
*   **volume** `configMap` o `secret` montados como ficheros (permisos `defaultMode`).

Los Secret de tipo `Opaque` son base64 en YAML solo para transporte; **no es cifrado**.

@section: Actualización

Al cambiar un ConfigMap montado como volumen, los ficheros se **actualizan** en caliente (tras un breve delay). Variables de entorno **no** se refrescan sin reiniciar el Pod.

@section: Buenas prácticas

*   **RBAC** estricto sobre `secrets`.
*   **External Secrets Operator** o integración con Vault/Cloud KMS para rotación.
*   No commitear Secrets reales en Git; usa **Sealed Secrets** o SOPS si deben vivir en repo cifrado.

@section: kubectl

```bash
kubectl create configmap app-config --from-file=app.properties
kubectl create secret generic db --from-literal=password=...
```

@quiz: Si montas un ConfigMap como volumen y actualizas el ConfigMap, ¿qué ocurre con los ficheros en el Pod?
@option: Nunca cambian hasta recrear la imagen
@correct: Suelen actualizarse en el volumen sin recrear el Pod (con latencia)
@option: Kubernetes reinicia siempre el nodo entero
