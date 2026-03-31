
@title: Objetos de la API
@icon: 📋
@description: apiVersion, kind, metadata, spec y status; etiquetas y selectores.
@order: 4

# Objetos en la API de Kubernetes

Todo recurso persistido en etcd es un **objeto**: tiene **apiVersion**, **kind**, **metadata**, **spec** (deseo) y **status** (observado, rellenado por el sistema).

@section: Estructura YAML mínima

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ejemplo
  namespace: default
  labels:
    app: demo
spec:
  containers:
    - name: app
      image: nginx:1.27-alpine
```

`metadata.name` y `metadata.namespace` identifican el objeto dentro de ese tipo y API. Algunos recursos son **con ámbito de namespace**; otros son **cluster-scoped** (`Node`, `ClusterRole`, `PersistentVolume`…).

@section: Labels y selectors

Las **labels** son pares clave/valor indexados y usados por **selectors** (Deployments → Pods, Services → Pods). Convenciones: `app`, `tier`, `version`, `team`, etc. Las **annotations** guardan metadatos no selectables (build id, contacto, políticas de red).

@section: Finalizers y ownerReferences

Los **finalizers** retrasan el borrado hasta que un controlador complete limpieza. **ownerReferences** enlazan objetos dependientes (un Pod creado por un ReplicaSet apunta a su dueño) para **cascada** al borrar el padre.

@section: Descubrir versiones

Cada recurso vive en un **group** (`apps`, `networking.k8s.io`…). Usa:

```bash
kubectl api-resources
kubectl explain deployment --api-version=apps/v1
```

@quiz: ¿Qué campo describe el estado observado que rellenan los controladores?
@option: spec
@correct: status
@option: metadata.uid solamente
