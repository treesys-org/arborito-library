@title: Seguridad de imágenes: supply chain y escaneo
@icon: 🖼️
@description: Firmas, bases mínimas, usuarios no root y políticas de admisión.
@order: 4

# Seguridad de imágenes: confiar en lo que despliegas

Las **imágenes** OCI son los artefactos que despliegas en Kubernetes. **Supply chain** moderna exige **firmas** (cosign), **SBOM**, **escaneo de vulnerabilidades** y **políticas** que bloqueen imágenes no autorizadas. Esta lección conecta conceptos sin atarte a un solo vendor.

@section: Bases de imagen

Imágenes **minimal** (distroless, alpine, wolfi) reducen superficie. **Actualiza** bases regularmente; vulnerabilidades en `glibc`/`openssl` son comunes.

**Usuarios:** no ejecutes como root en Dockerfile (`USER`).

@section: ImagePullSecrets y registros privados

**imagePullSecrets** referencia credenciales para registries privados. Para **CI**, preferir **OIDC** o tokens de corta duración en lugar de passwords largos.

@section: Escaneo

Herramientas **Trivy**, **Grype**, **Clair** escanean capas en CI. **Gate** en PR si severidad > umbral.

**Falsos positivos:** gestiona políticas de excepción con justificación.

@section: Firmas y verificación

**Sigstore/cosign** firma imágenes y verifica en admisión con **policy controllers** (Kyverno, OPA Gatekeeper, admission webhook).

**Digest pinning:** `image@sha256:...` en lugar de tags mutables.

@section: Políticas de admisión

**Kyverno** puede exigir labels, `runAsNonRoot`, prohibir `latest`, o verificar firmas.

**OPA Gatekeeper** con constraints Rego para reglas complejas.

@section: Errores frecuentes

* Tags `latest` en producción sin trazabilidad.
* Imágenes multi-arquitectura mal construidas → `ImagePullBackOff` en arm64.

@section: Laboratorio sugerido

1. Escanea una imagen pública con Trivy y revisa el informe.
2. Firma una imagen de prueba con cosign y documenta el verificador.
3. Escribe una política Kyverno/OPA que rechace `latest` en namespace `prod`.

@quiz: ¿Por qué fijar digest SHA en lugar de solo tag de imagen?
@option: Porque Kubernetes no soporta tags
@correct: Porque el tag puede moverse y cambiar el contenido sin que el manifiesto lo refleje claramente
@option: Porque acelera pulls
