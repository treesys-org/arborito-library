@title: Relaciones de Confianza (Trusts)
@icon: 🤝
@description: Cómo hacer que diferentes dominios y bosques se comuniquen entre sí.
@order: 4

# Relaciones de confianza (trusts)

Una **relación de confianza** permite que usuarios de un dominio **autentiquen** y accedan a recursos en otro dominio o bosque. En **multidominio** del mismo bosque, las confianzas **transitivas** existen por diseño. Entre **bosques** se crean **trusts explícitos**.

@section: Objetivos didácticos

*   Distinguir **unidireccional** vs **bidireccional**.
*   Entender **transitividad** dentro del bosque.
*   Nombrar escenarios: **forest trust**, **external trust**, **realm trust**.

@section: Dirección y sentido

*   **Dominio A confía en Dominio B:** los usuarios de B pueden acceder a recursos en A (según permisos).
* **Bidireccional:** ambos dominios se confían mutuamente.

En laboratorio, documenta siempre **quién** es el dominio **trusted** y **trusting**.

@section: Trusts entre bosques

*   **Forest trust:** entre dos bosques; puede ser **transitiva** dentro de cada bosque.
*   **External trust:** entre dos dominios **sin** transitividad al resto del bosque (útil para migraciones puntuales).

**Requisitos:** resolución DNS condicional o **forwarders** entre espacios de nombres; firewalls abiertos para Kerberos/LDAP/RPC según Microsoft.

@section: Selective authentication

En trusts entre bosques puedes activar **autenticación selectiva**: solo equipos/cuentas explícitas pueden autenticar a través del trust (reduce superficie de ataque).

@section: Kerberos y SPN

Los trusts dependen de **Kerberos**. Problemas típicos:

*   **Tiempo desincronizado** entre bosques.
*   **SPN** duplicados o mal configurados en servicios.
*   **DNS** que no resuelve los controladores del otro lado.

Herramientas: `nltest`, `netdom trust`, logs de **Kerberos** en visor de eventos.

@section: Caso de aula

Describe en dos párrafos:

1.  Por qué una fusión de empresas (`contoso.com` + `fabrikam.com`) podría requerir **forest trust** temporal.
2.  Qué equipo documentarías para soporte: **forwarders DNS**, **firewall**, **nivel funcional** del bosque.

@quiz: ¿Qué tipo de trust es típico para conectar dos bosques completos de Active Directory?
@option: External trust únicamente
@correct: Forest trust
@option: Shortcut trust dentro del mismo árbol

@quiz: ¿Qué protocolo de autenticación usa principalmente Active Directory para trusts entre dominios Windows?
@option: NTLM solo
@correct: Kerberos
@option: HTTP Basic
