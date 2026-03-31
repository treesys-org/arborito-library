@title: Entra ID (Azure AD): Hybrid Identity
@icon: ☁️
@description: Connecting on-premises AD to Microsoft cloud identity.
@order: 5

# Entra ID hybrid identity

**Microsoft Entra ID** powers Microsoft 365 and cloud sign-in. Most enterprises run **hybrid** with **Microsoft Entra Connect** syncing from on-premises AD.

@section: Connect

Entra Connect installs on a dedicated server, filters OUs/objects, and syncs hashes or uses **federation**/**pass-through** authentication models.

@section: Password Hash Sync

Replicates password hashes for cloud authentication—enables **Identity Protection** leaked credential detection when configured.

@section: Conditional Access

Cloud policies for **MFA**, **compliant devices**, and **named locations**—complements on-prem GPO for SaaS apps.

@section: Deprovisioning

Offboarding must cover **AD**, **Entra ID**, and **licenses** (M365) to avoid orphaned cloud accounts.

@quiz: Which tool syncs on-premises AD to Entra ID?
@option: WSUS
@correct: Microsoft Entra Connect (formerly Azure AD Connect)
@option: Hyper-V Manager

@quiz: What does Password Hash Sync enable in addition to cloud sign-in?
@option: Removes DNS
@correct: Cloud-based authentication and identity protection features (when configured)
@option: Avoids MFA entirely
