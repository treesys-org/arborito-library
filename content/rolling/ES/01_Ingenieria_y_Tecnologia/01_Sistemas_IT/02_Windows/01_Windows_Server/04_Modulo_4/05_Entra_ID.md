@title: Entra ID (Azure AD): Sincronización Híbrida
@icon: ☁️
@description: Conectando tu Active Directory local con la nube de Microsoft.
@order: 5

# Entra ID (antes Azure AD) e identidad híbrida

**Microsoft Entra ID** es el directorio en la nube que sustenta Microsoft 365, acceso condicional y dispositivos unidos a la nube. La mayoría de empresas **no** migran de golpe: usan **sincronización híbrida** desde AD local mediante **Microsoft Entra Connect** (anteriormente Azure AD Connect).

@section: Objetivos didácticos

*   Distinguir **cloud-only**, **synced** y **federated** (AD FS).
*   Entender **hash de contraseña**, **pass-through** y **SSO**.
*   Nombrar requisitos de **alta disponibilidad** del servidor de sincronización.

@section: Escenarios

1.  **Cloud-only:** usuarios creados solo en Entra ID (sin AD local).
2.  **Híbrido sincronizado:** cuentas locales replicadas a la nube (atributos + hash o autenticación federada).
3.  **Híbrido unido:** dispositivos Windows registrados en Entra ID con políticas MDM.

@section: Entra Connect

Componente instalado en un servidor Windows miembro del dominio:

*   **Sync** programada de usuarios, grupos y contactos.
*   **Filtros** por OU o atributo.
*   **Soft matching** / **hard matching** para evitar duplicados.

**Importante:** el servidor de Connect es **crítico**; planifica **alta disponibilidad** o procedimientos de recuperación.

@section: Autenticación híbrida

*   **Password Hash Sync (PHS):** réplica del hash para autenticar en la nube; habilita **detección de filtraciones**.
*   **Pass-through Authentication (PTA):** la autenticación contra AD local en tiempo real (agentes ligeros).
*   **Federation con AD FS:** SSO avanzado y requisitos de certificados; más operación.

@section: Acceso condicional

En la nube puedes exigir **MFA**, **dispositivos conformes** y **ubicaciones de confianza**. Las GPO locales **no** sustituyen estas políticas para apps SaaS.

@section: Limpieza de cuentas

Al desprovisionar usuarios, el flujo debe cubrir **AD local**, **Entra ID** y **licencias M365** (automatización vía scripts o HR-driven provisioning).

@section: Práctica conceptual

Redacta un diagrama textual:

*   DC local → **Entra Connect** → **Entra ID** → **Microsoft 365**.

Incluye **punto único de fallo** y cómo mitigarlo.

@quiz: ¿Qué herramienta de Microsoft sincroniza usuarios y grupos desde Active Directory local hacia Entra ID?
@option: WSUS
@correct: Microsoft Entra Connect (anteriormente Azure AD Connect)
@option: Hyper-V Manager

@quiz: ¿Qué ventaja ofrece Password Hash Sync frente a depender solo de federación en algunos escenarios?
@option: Elimina la necesidad de DNS
@correct: Permite autenticación en la nube aunque los controladores locales estén temporalmente inaccesibles (según configuración) y habilita protección de identidad
@option: Evita el uso de MFA
