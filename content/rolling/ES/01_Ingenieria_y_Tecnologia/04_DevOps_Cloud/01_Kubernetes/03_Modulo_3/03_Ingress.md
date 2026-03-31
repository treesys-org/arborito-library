
@title: Ingress y controladores
@icon: 🌐
@description: HTTP/HTTPS entrante, reglas host/path, TLS y comparación con Service LoadBalancer.
@order: 3

# Ingress

Un **Ingress** es un recurso que describe **enrutamiento L7** (HTTP/HTTPS) hacia Services internos. No abre puertos por sí mismo: necesitas un **Ingress Controller** (nginx, traefik, contour, ALB/GLB integrados…) que implemente esas reglas.

@section: Recurso Ingress

```yaml
spec:
  rules:
    - host: app.ejemplo.com
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

`pathType: Prefix|Exact|ImplementationSpecific` afecta cómo el controlador hace match.

@section: TLS

Referencia **Secret** tipo `kubernetes.io/tls` con `tls.crt` y `tls.key`. **cert-manager** automatiza ACME/Let's Encrypt rotando certificados.

@section: Ingress vs Service LoadBalancer

*   **LoadBalancer Service:** un VIP L4 por servicio; coste y cardinalidad altos.
*   **Ingress:** un punto de entrada (o pocos) que multiplexa muchos hosts/rutas.

@section: Gateway API

La **Gateway API** (successor conceptual) modela rutas más expresivas y roles RBAC separados (infra vs app). Coexiste con Ingress en muchos clústeres.

@quiz: ¿Qué componente debe estar instalado para que las reglas de un objeto Ingress surtan efecto?
@option: Solo kube-proxy
@correct: Un Ingress Controller que observe recursos Ingress
@option: El scheduler
