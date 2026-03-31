
@title: Helm: charts y releases
@icon: ⎈
@description: Plantillas Go, values, upgrade/rollback y dependencias.
@order: 1

# Helm

**Helm** empaqueta manifiestos Kubernetes en **charts** versionados: plantillas YAML con **Go templates**, valores externos (`values.yaml`) y ciclo de vida de **releases** en el clúster (almacenadas como Secrets/ConfigMaps según versión de Helm).

@section: Chart layout

```
Chart.yaml          # metadatos y versión del chart
values.yaml         # valores por defecto
templates/          # .yaml con {{ .Values.* }}
```

`helm install mi-release ./chart -f prod.yaml` crea objetos con nombres prefijados por release.

@section: Upgrade y rollback

```bash
helm upgrade mi-release ./chart --install
helm history mi-release
helm rollback mi-release 2
```

Usa **`--atomic`** para revertir automáticamente si falla el upgrade.

@section: Hooks y tests

**Hooks** (`pre-install`, `post-upgrade`…) ejecutan Jobs en momentos del ciclo. **`helm test`** lanza Pods de prueba definidos en el chart.

@section: OCI y repos

Charts pueden vivir en **registros OCI** (como imágenes). Sustituye parcialmente a `helm repo add` clásico.

@section: Cuándo no usar Helm

Para apps muy simples, **Kustomize** o manifiestos planos + GitOps pueden bastar. Helm añade complejidad de **templating** y de **gestión de valores** secretos.

@quiz: ¿Qué fichero suele contener los valores por defecto editables por entorno en un chart?
@option: templates/values.toml
@correct: values.yaml en la raíz del chart
@option: Chart.lock únicamente
