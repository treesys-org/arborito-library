
@title: Volúmenes y PersistentVolumeClaims
@icon: 💾
@description: emptyDir, hostPath, PVC/PV y modos de acceso.
@order: 1

# Almacenamiento en Pods

Los contenedores son efímeros: sin volúmenes, todo se pierde al reiniciar. Kubernetes abstrae el almacenamiento con **volúmenes** montados en el Pod y, para datos persistentes, con **PersistentVolume (PV)** y **PersistentVolumeClaim (PVC)**.

@section: Volúmenes efímeros

*   **emptyDir:** directorio vacío en el nodo (o memoria `medium: Memory`); se borra si el Pod se reubica.
*   **hostPath:** monta ruta del nodo—útil en nodo único o depuración; **peligroso** en multi-tenant (fuga de datos entre cargas).

@section: PVC y PV

El usuario declara un **PVC** (tamaño, `storageClassName`, `accessModes`). El **provisioner** dinámico crea el **PV** o se enlaza uno estático. El Pod monta el PVC por nombre.

```yaml
accessModes:
  - ReadWriteOnce   # un nodo a la vez en muchos entornos
```

`ReadWriteMany` depende del backend (NFS, CSI de filesystem compartido).

@section: subPath y proyectos

`subPath` monta un subdirectorio o fichero concreto dentro del volumen—útil para compartir un volumen entre contenedores con distintas rutas.

@section: Snapshots CSI

**VolumeSnapshot** permite copias coherentes según el driver CSI (bases, backups incrementales). Requiere **VolumeSnapshotClass** y CRDs instaladas.

@quiz: ¿Qué objeto solicita el usuario para obtener almacenamiento persistente enlazado a un Pod?
@option: PersistentVolume directamente siempre
@correct: PersistentVolumeClaim
@option: Solo emptyDir con tamaño en Gi
