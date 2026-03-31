
@title: DNS del clúster (CoreDNS)
@icon: 🔗
@description: Nombres de Services, headless, búsquedas y resolución entre namespaces.
@order: 5

# DNS en Kubernetes

**CoreDNS** (sustituto de kube-dns) responde consultas desde Pods. Cada Pod recibe **`/etc/resolv.conf`** apuntando al Service `kube-dns` y búsquedas **suffix** como `default.svc.cluster.local`.

@section: Nombres de Service

*   **`<svc>`** dentro del mismo namespace.
*   **`<svc>.<namespace>`** cross-namespace.
*   FQDN: **`<svc>.<namespace>.svc.cluster.local`**.

@section: Headless

`clusterIP: None` + selector: DNS devuelve **registros A/AAAA** por cada Pod listo, útil para descubrimiento de miembros en clúster.

@section: ExternalName

Service tipo **ExternalName** devuelve un **CNAME** a un host DNS externo sin crear Endpoints—simple alias.

@section: Depuración

```bash
kubectl run -it --rm debug --image=busybox:1.36 --restart=Never -- nslookup kubernetes.default
```

Fallos habituales: **NetworkPolicy** bloqueando UDP 53, **CoreDNS** OOM, o `ndots` alto provocando búsquedas lentas en nombres externos.

@quiz: ¿Cuál es el sufijo DNS por defecto de un Service en el namespace `prod`?
@option: prod.cluster.local únicamente
@correct: prod.svc.cluster.local (FQDN completo incluye svc)
@option: kubernetes.default
