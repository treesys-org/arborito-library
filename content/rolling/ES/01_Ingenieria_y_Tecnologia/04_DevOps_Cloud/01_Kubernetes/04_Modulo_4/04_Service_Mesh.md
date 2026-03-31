
@title: Service mesh (visión operativa)
@icon: 🕸️
@description: Dataplane lateral, mTLS, tráfico y coste operativo.
@order: 4

# Service mesh

Un **mesh** inserta un **sidecar proxy** (Envoy es común) junto a cada Pod para interceptar tráfico east-west. **Istio**, **Linkerd**, **Consul Connect** son implementaciones conocidas.

@section: Qué problema resuelve

*   **mTLS** entre servicios sin tocar código.
*   **Retries, timeouts, circuit breaking** uniformes.
*   **Observabilidad** L7 (rutas HTTP, códigos) y **trazas** con headers correlación.

@section: Costes

*   CPU/mem **extra** por sidecar.
*   Complejidad de **CRDs** y **control planes**.
*   Depuración más difícil si no entiendes el proxy.

@section: Cuándo adoptarlo

Cuando el número de servicios y equipos crece y los **libraries** de resiliencia están duplicadas o incumplidas. Para dos microservicios, a menudo **basta** buen CNI + políticas + observabilidad.

@section: Sidecar vs eBPF

Algunos enfoques (Cilium Gateway + policies, o ambient mesh de Istio) reducen sidecars; el mercado evoluciona rápido—evalúa **tu** restricción de kernel y multi-tenant.

@quiz: ¿Qué pieza intercepta típicamente el tráfico TCP del contenedor de aplicación en un mesh con sidecar?
@option: kube-scheduler
@correct: Un proxy sidecar (p. ej. Envoy) en el mismo Pod
@option: Solo iptables en el laptop del desarrollador
