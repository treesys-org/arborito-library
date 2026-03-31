
@title: Cluster DNS (CoreDNS)
@icon: 🔗
@description: Service names, headless records, searches, cross-namespace queries.
@order: 5

# Cluster DNS

**CoreDNS** answers queries from Pods. Each Pod gets **`/etc/resolv.conf`** pointing at the `kube-dns` Service plus search suffixes like `default.svc.cluster.local`.

@section: Service DNS names

*   **`<svc>`** inside the same namespace.
*   **`<svc>.<namespace>`** across namespaces.
*   FQDN: **`<svc>.<namespace>.svc.cluster.local`**.

@section: Headless Services

`clusterIP: None` with a selector: DNS returns **A/AAAA** records for each ready Pod—useful for StatefulSet member discovery.

@section: ExternalName

**ExternalName** Services return a **CNAME** to an external hostname—simple DNS alias without Endpoints.

@section: Debugging

```bash
kubectl run -it --rm debug --image=busybox:1.36 --restart=Never -- nslookup kubernetes.default
```

Common failures: **NetworkPolicy** blocking UDP 53, CoreDNS OOM, high **`ndots`** slowing external lookups.

@quiz: What is the default DNS suffix for a Service in namespace `prod`?
@option: prod.cluster.local only
@correct: prod.svc.cluster.local (full FQDN includes svc)
@option: kubernetes.default
