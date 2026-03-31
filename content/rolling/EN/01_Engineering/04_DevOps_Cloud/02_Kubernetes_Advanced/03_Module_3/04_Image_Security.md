@title: Image Security: Supply Chain and Scanning
@icon: 🖼️
@description: Signing, minimal bases, non-root users, and admission policies.
@order: 4

# Container image security: trust what you deploy

**OCI images** are what you deploy to Kubernetes. Modern **supply chain** practice expects **signatures** (cosign), **SBOMs**, **vulnerability scanning**, and **policies** blocking unauthorized images. This lesson ties concepts together without locking you to one vendor.

@section: Base images

**Minimal** bases (distroless, alpine, wolfi) reduce surface area. **Update** bases regularly; `glibc`/`openssl` vulnerabilities are common.

**Users:** do not run as root in Dockerfile (`USER`).

@section: ImagePullSecrets and private registries

**imagePullSecrets** reference credentials for private registries. For **CI**, prefer **OIDC** or short-lived tokens over long passwords.

@section: Scanning

**Trivy**, **Grype**, **Clair** scan layers in CI. **Gate** PRs if severity exceeds a threshold.

**False positives:** manage exception policies with justification.

@section: Signing and verification

**Sigstore/cosign** signs images and admission verifies with **policy controllers** (Kyverno, OPA Gatekeeper, admission webhooks).

**Digest pinning:** `image@sha256:...` instead of mutable tags.

@section: Admission policies

**Kyverno** can require labels, `runAsNonRoot`, ban `latest`, or verify signatures.

**OPA Gatekeeper** with Rego constraints for complex rules.

@section: Common mistakes

* `latest` tags in production without traceability.
* Multi-arch images built incorrectly → `ImagePullBackOff` on arm64.

@section: Suggested lab

1. Scan a public image with Trivy and review the report.
2. Sign a test image with cosign and document verification.
3. Write a Kyverno/OPA policy rejecting `latest` in namespace `prod`.

@quiz: Why pin digest SHA instead of only an image tag?
@option: Kubernetes does not support tags
@correct: Tags can move and change content without the manifest clearly reflecting it
@option: Because pulls are faster
