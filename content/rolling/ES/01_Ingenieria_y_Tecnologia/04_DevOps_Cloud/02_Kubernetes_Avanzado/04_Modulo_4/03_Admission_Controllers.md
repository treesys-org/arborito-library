@title: Admission controllers y webhooks
@icon: 🚧
@description: Validating y mutating, orden de ejecución y disponibilidad.
@order: 3

# Admission: validar y mutar antes de persistir

Los **admission controllers** interceptan solicitudes al apiserver **después** de autenticación/autorización y **antes** de escribir en etcd. Los **webhooks** externos (validating/mutating) permiten políticas personalizadas (OPA, Kyverno, Istio sidecar injection). Esta lección cubre **orden**, **fail-open vs fail-closed** y **alta disponibilidad**.

@section: Mutating vs validating

* **Mutating:** puede cambiar el objeto (inyectar sidecar, labels).
* **Validating:** solo aprueba o rechaza.

**Orden:** mutating primero, luego validating (simplificado; revisa la versión).

@section: WebhookConfiguration

`ValidatingWebhookConfiguration` / `MutatingWebhookConfiguration` definen:

* **clientConfig** (URL o service)
* **rules** (recursos/verbos)
* **failurePolicy** `Fail` o `Ignore`
* **timeoutSeconds**
* **sideEffects** / **matchPolicy**

**CA bundle** debe confiar el apiserver en el servicio webhook.

@section: Disponibilidad

Si el webhook está caído y `failurePolicy: Fail`, **todo** create/update bloqueado → incidente grave.

**Estrategias:** réplicas múltiples, **podDisruptionBudget**, **timeouts** razonables, **FailOpen** solo si aceptas riesgo de compliance.

@section: Políticas comunes

* Requerir `resources` mínimos.
* Bloquear `privileged` pods.
* Verificar firmas de imagen.

@section: Errores frecuentes

* Webhook lento → latencia global en API.
* Certificados TLS mal rotados en el servicio webhook.

@section: Laboratorio sugerido

1. Lista webhooks en tu cluster (`kubectl get validatingwebhookconfiguration`).
2. Revisa una política Kyverno/OPA existente y su traducción a webhook.
3. Simula caída del webhook en staging y observa el impacto.

@quiz: ¿Qué diferencia principal hay entre mutating y validating admission?
@option: Solo el nombre
@correct: Mutating puede modificar el objeto; validating solo acepta o rechaza
@option: Validating se ejecuta antes de autenticación
