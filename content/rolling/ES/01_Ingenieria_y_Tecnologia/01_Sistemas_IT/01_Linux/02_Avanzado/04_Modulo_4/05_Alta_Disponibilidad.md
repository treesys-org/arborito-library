@title: Alta Disponibilidad: Clusters y Balanceadores de Carga
@icon: ⚖️
@description: Diseñando sistemas que nunca se caen. Conceptos de VIP (IP Virtual), Failover con Keepalived y Balanceo con HAProxy.
@order: 5

# Alta disponibilidad: VRRP, balanceo y clusters Pacemaker

Eliminar el **punto único de fallo** requiere redundancia de **hardware**, **red** y **datos**. Aquí unimos **Keepalived (VRRP)** para IPs flotantes con **HAProxy/Nginx** como balanceadores y una mirada a **Pacemaker/Corosync** para servicios en cluster.

@section: 1. VIP y VRRP

**VRRP** permite que varios nodos compartan una **IP virtual (VIP)**; solo el **master** responde. Si cae, el **backup** asume tras unos segundos.

**Keepalived** (`/etc/keepalived/keepalived.conf`):

*   `state MASTER` / `BACKUP`
*   `priority` (mayor = preferido)
*   `virtual_router_id` único en la LAN
*   `virtual_ipaddress { ... }`

**Health checks:** scripts `vrrp_script` que bajan la prioridad si el servicio local falla.

@section: 2. Balanceo de carga (capa 4 y 7)

**HAProxy** (L4 TCP / L7 HTTP):

*   Algoritmos: `roundrobin`, `leastconn`, `source` (sticky).
*   **Health checks:** `option httpchk`, `check` en backends.

**Nginx/HAProxy** delante de varios **upstream** aplicados.

**Diseño activo-activo:** todos los nodos reciben tráfico; **activo-pasivo** con VIP solo en servicios legacy.

**Ejemplo mínimo HAProxy (conceptual):** un `frontend` escucha en `:443`, un `backend` lista tres servidores con `check inter 2s fall 3 rise 2`. Si dos health checks fallan, el nodo se marca *down* y el tráfico va al resto. El **stats socket** (`stats socket /run/haproxy/admin.sock`) permite inspeccionar estado en caliente sin reiniciar.

**TLS passthrough vs terminación:** puedes balancear TCP puro (443 → backends 443) o terminar TLS en HAProxy (certificados en el balanceador). La primera opción deja el cifrado hasta la app; la segunda centraliza certificados y CPU criptográfica.

@section: 3. Pacemaker y Corosync (cluster de servicios)

Para **bases de datos** o recursos que no pueden duplicarse trivialmente, usa **Pacemaker** + **Corosync**:

*   **Quorum:** mayoría de nodos vivos (evita split-brain).
*   **STONITH / fencing:** apagar el nodo que pierde quorum (IPMI, iDRAC).
*   **Recursos:** IP flotante, servicio `systemd:nginx`, **DRBD** para disco compartido.

**Complejidad alta:** mal configurado, produce más caídas que beneficios.

**Quorum en números:** con **tres nodos**, toleras la caída de uno y mantienes mayoría (2≥2). Con **dos nodos**, un fallo de red entre ellos genera **split-brain** si no hay **fencing** o **witness** (tercera opinión). Por eso los clusters serios usan número impar de nodos o arbitraje externo.

**STONITH en la práctica:** apagar el nodo perdedor vía **iDRAC/IPMI**, **fence_vmware**, **fence_aws**… La elección depende de dónde viva la máquina. Sin fencing, dos nodos pueden creer ambos ser dueños del recurso y corromper datos (especialmente con sistemas de archivos que no son cluster-aware).

@section: 4. DNS como balanceo rudimentario

**Round-robin** en múltiples A records: simple pero **no** quita nodos caídos sin health checks (usa **GSLB** o servicios cloud).

@section: 5. Datos compartidos

HA de aplicación requiido **almacenamiento compartido** (SAN, NFS con cuidado, bases replicadas). **DRBD** replica bloque entre dos nodos.

@section: 6. Observabilidad

Monitoriza VIP (ping), endpoints HTTP, y métricas de HAProxy (`stats socket`). **Alertas** en Prometheus/Grafana.

@section: 7. RHEL vs Debian

*   **RHEL High Availability Add-On** documenta Pacemaker + fence agents.
*   **Keepalived** y **HAProxy** empaquetados en todas las familias.

@section: 8. Nube y “balanceadores gestionados”

En AWS (**ELB/ALB**), GCP (**Cloud Load Balancing**) o Azure, el plano de control del balanceador es responsabilidad del proveedor: health checks, TLS y escala horizontal. Sigue siendo útil conocer **Keepalived/HAProxy** porque en **Kubernetes** (MetalLB, kube-vip) o **on-prem** sigues montando el mismo modelo mental: VIP + health checks + backends.

@section: 9. Laboratorio ampliado

1.  Dos VMs con Keepalived y misma VIP; apaga el master y mide el tiempo de conmutación (segundos típicos: 1–5 según `advert_int` y red).
2.  Documenta el riesgo de **split-brain** si el enlace entre nodos falla pero ambos siguen vivos; propón mitigación (fencing, quorum device, o tercer nodo).
3.  Configura HAProxy en una VM delante de dos backends HTTP estáticos y observa el comportamiento al parar uno (`ss` + logs).
4.  Escribe en dos párrafos cuándo elegirías **activo-activo** vs **activo-pasivo** para una base de datos relacional tradicional.

@quiz: ¿Qué protocolo usa Keepalived para mover una IP virtual entre nodos?
@option: BGP
@correct: VRRP
@option: ARP

@quiz: ¿Qué mecanismo evita que dos nodos de cluster escriban en el mismo recurso corruptándolo?
@option: DHCP
@correct: Quorum + fencing (STONITH)
@option: cron

@quiz: ¿Qué componente suele hacer health checks HTTP a los backends en un balanceador?
@option: bind
@correct: HAProxy (u otro LB) con directivas `check`
@option: tcpdump
