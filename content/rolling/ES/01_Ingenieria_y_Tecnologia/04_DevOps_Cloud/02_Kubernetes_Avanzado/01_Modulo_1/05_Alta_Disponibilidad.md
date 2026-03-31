@title: Alta disponibilidad del plano de control
@icon: 🏗️
@description: Topologías etcd, balanceo de kube-apiserver y trade-offs de coste.
@order: 5

# Alta disponibilidad del plano de control

Un clúster **production-grade** necesita que el **plano de control** sobreviva fallos de nodo y cortes parciales de red. Esta lección resume **topologías** (stacked vs externos), **balanceo de apiserver**, **etcd quorum** y trade-offs de coste sin entrar en el manual de cada distribución.

@section: Componentes del control plane

* **kube-apiserver:** frente HTTP(S) a la API.
* **etcd:** almacén de estado.
* **kube-scheduler:** asigna pods a nodos.
* **kube-controller-manager:** controladores (Deployment, ReplicaSet, etc.).
* **cloud-controller-manager:** integración cloud (LB, rutas) según entorno.

En cloud gestionado muchos de estos detalles están **ocultos** pero siguen existiendo bajo el capó.

@section: etcd y quorum

etcd requiere **quorum** mayoritario: típicamente **3** nodos toleran 1 fallo; **5** toleran 2. Número **impar** de miembros.

**No** escales etcd a muchos miembros sin guía: más nodos ≠ más rendimiento de lectura/escritura lineal.

@section: kube-apiserver HA

Varias instancias de **apiserver** detrás de un **load balancer** TLS (interno). Los clientes (kubelets, usuarios) apuntan al VIP o DNS del balanceador.

Certificados deben incluir **SAN** para el nombre del LB y posiblemente IPs.

@section: Stacked etcd vs externos

* **Stacked:** etcd corre en los mismos hosts que el control plane (kubeadm típico pequeño).
* **External etcd:** cluster dedicado, útil en grandes despliegues o separación operativa.

Cloud providers a menudo gestionan etcd de forma **opaca** con SLA.

@section: Impacto en workloads

Alta disponibilidad del plano de control **no** sustituye HA de **aplicaciones**: necesitas réplicas, PDB, storage replicado, y diseño stateless donde sea posible.

@section: Zonas y regiones

* **Multi-AZ** dentro de una región: combate fallos de AZ; latencia baja.
* **Multi-región** activo-activo es **mucho** más difícil (bases de datos, consistencia); a menudo activo-pasivo o sharding.

@section: Errores frecuentes

* Un solo apiserver sin LB (SPOF lógico o de red).
* Backups de etcd sin **probar** restore.
* Subdimensionar etcd en IOPS (latencia del plano entero sufre).

@section: Laboratorio sugerido

1. Dibuja en papel un plano de control de 3 nodos con LB frente a apiservers y etcd de 3 miembros.
2. Enumera qué falla primero si pierdes 1 nodo vs 2 nodos de etcd en un cluster de 3.
3. Lee el SLA de tu proveedor cloud sobre el plano de control y compáralo con tus SLO de aplicación.

@quiz: ¿Por qué los clústeres etcd suelen tener un número impar de miembros?
@option: Porque Kubernetes lo exige para todos los pods
@correct: Para mayorías de votación (quorum) en el consenso Raft
@option: Porque los pares son más baratos
