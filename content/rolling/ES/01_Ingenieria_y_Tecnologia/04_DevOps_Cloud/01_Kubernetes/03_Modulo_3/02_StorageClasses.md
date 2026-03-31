
@title: StorageClasses y aprovisionamiento
@icon: 📦
@description: Provisionamiento dinámico, reclaimPolicy y default StorageClass.
@order: 2

# StorageClass

Una **StorageClass** describe **cómo** se crean volúmenes: proveedor CSI, tipo de disco (SSD/HDD), cifrado, expansión, etc. El campo `provisioner` apunta al plugin (por ejemplo `ebs.csi.aws.com`).

@section: Aprovisionamiento dinámico

Cuando un PVC especifica `storageClassName`, el controlador crea el PV automáticamente. Si el PVC deja el campo vacío y existe una SC **por defecto** (anotación `storageclass.kubernetes.io/is-default-class: "true"`), se usa esa.

@section: reclaimPolicy

*   **Delete:** al borrar el PVC, el PV y el recurso cloud subyacente se eliminan (según driver).
*   **Retain:** conserva el disco para recuperación manual—útil en datos críticos.

@section: VolumeBindingMode

`WaitForFirstConsumer` retrasa el aprovisionamiento hasta que un Pod programado necesite el volumen—evita volúmenes en zonas que no coinciden con el nodo.

@section: Expansión

Muchos drivers permiten **expandir** PVCs (`allowVolumeExpansion: true`) sin recrear el Pod, aunque el filesystem dentro del contenedor puede requerir `resize2fs` u operador.

@quiz: ¿Qué anotación marca una StorageClass como predeterminada para PVCs sin storageClassName?
@option: kubernetes.io/default-storage
@correct: storageclass.kubernetes.io/is-default-class: "true"
@option: provisioner.kubernetes.io/default
