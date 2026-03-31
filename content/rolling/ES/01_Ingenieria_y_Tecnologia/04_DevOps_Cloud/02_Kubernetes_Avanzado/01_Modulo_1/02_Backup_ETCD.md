@title: Respaldo y restauración de etcd
@icon: 💾
@description: Snapshots, consistencia, restauración del control plane y pruebas periódicas.
@order: 2

# etcd: respaldo y restauración del estado del clúster

**etcd** almacena todo el estado del clúster: objetos de la API, configuración y metadatos. Si pierdes etcd sin backup coherente, **recrear** el clúster desde cero es doloroso. Esta lección cubre snapshots, **consistencia**, procedimientos de restore y por qué «copiar /var/lib/etcd» a mano no basta.

@section: Rol de etcd

etcd es una base **clave-valor** distribuida con **consenso Raft**. Solo los miembros del clúster etcd deben acceder a los datos; el apiserver es el cliente principal.

**Alta disponibilidad:** típicamente 3 o 5 miembros impares en nodos distintos de fallo.

@section: Snapshots oficiales

La herramienta **`etcdctl snapshot save`** genera un archivo de snapshot en un punto consistente cuando se usa correctamente contra el endpoint certificado del clúster.

Pasos conceptuales:

1. Usar **certificados** y **endpoints** correctos (TLS).
2. Guardar el snapshot en almacenamiento **externo** (S3, GCS) con cifrado y retención.
3. **Probar restores** en un entorno aislado, no solo «confiar» en el archivo.

@section: Frecuencia y RPO

Define **objetivo de punto de recuperación (RPO)**: ¿cuánto estado puedes perder? Snapshots cada hora vs cada día cambian el riesgo. Combina snapshots con **backups de configuración** GitOps (manifiestos) para reconstruir lo desplegable.

@section: Restauración

Restore **no** es siempre «reemplazar carpeta y arrancar»: depende de si etcd es **stacked** con control plane o externo, y de tu herramienta (kubeadm, operadores de cloud).

**Procedimiento típico:** detener apiserver/kube-scheduler/controller-manager en miembros afectados, restaurar snapshot con `etcdctl snapshot restore`, reconfigurar `--initial-cluster`, arrancar en orden documentado.

**Importante:** un restore puede afectar **todo** el clúster; coordina ventana y comunicación.

@section: Velero y backup de workloads

**Velero** respalda recursos Kubernetes y volúmenes (según drivers); complementa etcd para **recuperación de namespaces** o migraciones. No sustituye por completo el snapshot de etcd en escenarios de desastre del plano de control.

@section: Cifrado en reposo

Habilita **encryption at rest** en kube-apiserver para secretos sensibles además de backups de etcd (defensa en profundidad).

@section: Errores frecuentes

* Snapshots sin verificar restauración (archivo corrupto o inútil).
* Mezclar snapshots de distintos miembros sin entender **quorum**.
* Guardar backups en el mismo sitio que el clúster sin copia off-site.

@section: Laboratorio sugerido

1. En un cluster de laboratorio con etcd accesible, ejecuta un snapshot y documenta los comandos exactos.
2. Restaura en **otro** cluster de prueba siguiendo la guía oficial.
3. Anota el tiempo total de recuperación (RTO) medido.

@quiz: ¿Por qué no basta con copiar manualmente /var/lib/etcd sin procedimiento etcdctl?
@option: Porque ocupa mucho espacio
@correct: Porque necesitas un snapshot consistente y un procedimiento de restore compatible con la topología Raft y TLS del clúster
@option: Porque Kubernetes no usa etcd
