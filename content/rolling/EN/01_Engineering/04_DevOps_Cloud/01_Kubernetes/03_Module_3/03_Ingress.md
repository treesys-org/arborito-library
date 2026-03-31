
@title: Ingress and controllers
@icon: 🌐
@description: HTTP/HTTPS ingress, host/path rules, TLS, vs LoadBalancer Services.
@order: 3

# Ingress

An **Ingress** describes **layer-7** routing (HTTP/HTTPS) to internal Services. It does not open ports by itself—you need an **Ingress Controller** (nginx, traefik, contour, cloud ALB/GLB integrations…) to implement the rules.

@section: Ingress resource

```yaml
spec:
  rules:
    - host: app.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api-svc
                port:
                  number: 8080
```

`pathType` affects matching semantics (`Prefix`, `Exact`, `ImplementationSpecific`).

@section: TLS

Reference a **Secret** of type `kubernetes.io/tls` with `tls.crt` and `tls.key`. **cert-manager** automates ACME / Let’s Encrypt rotation.

@section: Ingress vs LoadBalancer Service

*   **LoadBalancer Service:** one L4 VIP per service—cost and cardinality.
*   **Ingress:** one (or few) entry points multiplexing many hosts/paths.

@section: Gateway API

**Gateway API** models richer routing and split RBAC (platform vs app). Coexists with Ingress in many clusters.

@quiz: What must be installed for Ingress objects to take effect?
@option: kube-proxy alone
@correct: An Ingress controller that reconciles Ingress resources
@option: The scheduler
