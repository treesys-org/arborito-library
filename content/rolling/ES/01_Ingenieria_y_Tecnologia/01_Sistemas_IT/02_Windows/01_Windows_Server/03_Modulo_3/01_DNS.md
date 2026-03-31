@title: DNS en Windows: Zonas e Integración AD
@icon: 🌐
@description: La resolución de nombres como pilar de Active Directory.
@order: 1

# DNS en Windows: zonas e integración con AD

**DNS** es el servicio que traduce nombres (`srv01.dominio.local`) a direcciones IP. Active Directory **depende** de DNS para localizar controladores (**registros SRV**). Un error de DNS se manifiesta como “no puedo unir al dominio”, “inicio de sesión lento” o “replicación fallida”.

@section: Objetivos didácticos

*   Crear **zonas primarias integradas en AD** y **zonas de resolución inversa**.
*   Explicar **reenvío (forwarders)** y **raíz (hints)**.
*   Diagnosticar con `nslookup`, `dcdiag` y visor de eventos.

@section: Zonas

*   **Primaria:** lectura/escritura en un servidor autoritativo.
*   **Secundaria:** copia de solo lectura por transferencia (AXFR/IXFR).
*   **Integrada en AD:** los datos se replican con AD (multi-master); recomendada para zonas del dominio.

@section: Registros habituales

| Tipo | Uso |
| :--- | :--- |
| **A / AAAA** | Host → IPv4 / IPv6 |
| **CNAME** | Alias |
| **SRV** | Ubicación de servicios (LDAP, Kerberos) |
| **MX** | Correo |
| **PTR** | Resolución inversa |

**Dynamic updates:** los clientes Windows pueden registrar sus registros (según política).

@section: Integración con AD

Al promover un DC con DNS integrado, se crean registros **SRV** bajo `_ldap._tcp.dc._msdcs.dominio`. Si borras zonas a mano, **rompes** el inicio de sesión.

**Regla:** los clientes deben usar **DNS que conozca la zona del dominio** (normalmente los DC).

@section: Forwarders

Configura **forwarders** hacia el DNS de tu ISP o **1.1.1.1/8.8.8.8** para nombres **externos**. El DNS interno resuelve `contoso.local`; el forwarder resuelve `microsoft.com`.

**DNSSEC** en entornos corporativos puede requerir validación adecuada en forwarders.

@section: Diagnóstico rápido

```powershell
nslookup srv01.dominio.local
dcdiag /test:dns
Get-DnsServerZone
```

@section: Práctica

1.  Crea un registro **A** para un servidor de prueba.
2.  Verifica resolución inversa **PTR** en la zona reversa.
3.  Simula fallo: DNS del cliente apuntando a un router doméstico sin zona AD → documenta el síntoma.

@quiz: ¿Qué tipo de registro DNS usa el cliente para localizar controladores de dominio LDAP?
@option: MX
@correct: SRV (junto con registros A del DC)
@option: TXT

@quiz: ¿Qué ventaja tiene una zona DNS integrada en Active Directory?
@option: Es más rápida que cualquier DNS de Linux
@correct: Se replica con AD y admite actualizaciones en varios DCs de forma multimaster
@option: No necesita registros
