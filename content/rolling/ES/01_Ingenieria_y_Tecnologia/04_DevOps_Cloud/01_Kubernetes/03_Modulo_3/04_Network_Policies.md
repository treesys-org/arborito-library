
@title: NetworkPolicies
@icon: 🛡️
@description: Segmentación L3/L4 entre Pods; default deny y reglas de flujo.
@order: 4

# Políticas de red

Por defecto, **todos los Pods pueden hablar con todos** en el clúster. Las **NetworkPolicy** (CNI que las soporte: Calico, Cilium, Weave…) restringen tráfico **entrante** (`ingress`), **saliente** (`egress`) o ambos.

@section: Selectores

Las políticas seleccionan Pods con `podSelector` y opcionalmente `namespaceSelector`. También puedes referir IPs externas (`ipBlock` con CIDR y `except`).

@section: Patrón default deny

1. Política que selecciona todos los Pods del namespace y **no** permite ingress (ni egress si aplicas deny saliente).
2. Políticas más específicas que **permiten** solo los flujos necesarios (front → api, api → db).

@section: DNS y egress

Si bloqueas todo egress, rompes **CoreDNS**. Suele añadirse regla explícita al Service `kube-dns` UDP/TCP 53.

@section: Observabilidad

Con políticas estrictas, los timeouts pueden ser **silenciosos**. Usa `kubectl exec` con `nc`, logs del CNI, o herramientas Hubble (Cilium) para ver drops.

@quiz: ¿Qué requisito previo tiene sentido para que NetworkPolicy funcione en un clúster?
@option: Solo usar Services tipo NodePort
@correct: Un CNI que implemente NetworkPolicy
@option: Desactivar kube-proxy siempre
