
@title: Imperativo frente a declarativo
@icon: ⚖️
@description: kubectl create/run vs apply; gestión declarativa y GitOps.
@order: 5

# Estilos de gestión

Puedes crear recursos con comandos **imperativos** (`kubectl run`, `kubectl create`) o con manifiestos **declarativos** (YAML + `kubectl apply`). En producción prima lo **declarativo** versionado en Git.

@section: Imperativo rápido

Útil para pruebas y depuración:

```bash
kubectl create deployment demo --image=nginx:1.27-alpine --replicas=2
kubectl expose deployment demo --port=80 --type=ClusterIP
```

Riesgo: difícil de **reproducir** y de auditar. No sustituye manifiestos en repo.

@section: Declarativo con apply

`kubectl apply -f manifest.yaml` hace **merge** en el campo `managedFields` (server-side apply en versiones recientes) y es **idempotente**. Patrón:

*   Un fichero o directorio por app.
*   `kustomize` o Helm para variantes (dev/stage/prod).

@section: Diff y validación

```bash
kubectl diff -f .
kubectl apply --dry-run=server -f deployment.yaml
```

`--dry-run=server` valida contra admission webhooks y API sin persistir.

@section: GitOps (visión)

Herramientas como **Argo CD** o **Flux** observan un repo y convergen el clúster al estado declarado. El **manifiesto en Git** es la fuente de verdad; los cambios manuales `kubectl edit` se consideran **deriva** a corregir.

@quiz: ¿Qué comando suele usarse para aplicar manifiestos de forma idempotente?
@option: kubectl create solo
@correct: kubectl apply -f …
@option: kubectl run obligatoriamente
