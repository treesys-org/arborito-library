@title: Conceptos: Bosques, Árboles y Dominios
@icon: 🌲
@description: Entendiendo la estructura jerárquica de Active Directory.
@order: 1

# Conceptos: bosques, árboles y dominios

**Active Directory Domain Services (AD DS)** es un servicio de directorio que almacena objetos (usuarios, equipos, grupos) y **autentica** el acceso a recursos. En instituto y en certificaciones Microsoft, debes dominar el vocabulario: **bosque**, **árbol**, **dominio**, **OU** y **confianza**.

@section: Objetivos didácticos

*   Definir **bosque**, **árbol**, **dominio** y **DC**.
*   Explicar el **DNS** como requisito de nombre.
*   Relacionar el **esquema** con extensiones de aplicaciones.

@section: Dominio

Un **dominio** es un **límite administrativo** y de seguridad: comparte una base de datos de directorio (un **contexto de nombres** DNS) y una **política de contraseñas** por defecto (aunque puede haber políticas granulares).

*   Ejemplo: `empresa.local` (nombre DNS del dominio).
*   Los **controladores de dominio** replican la información entre sí.

@section: Árbol

Un **árbol** es un conjunto de **dominios** con un espacio de nombres **contiguo** (mismo sufijo DNS en jerarquía).

*   Ejemplo `contoso.com` → `ventas.contoso.com`, `it.contoso.com`.

Todos comparten un **esquema** y **catálogo global** en el mismo bosque.

@section: Bosque

Un **bosque** es el **contenedor de seguridad** superior: un conjunto de uno o más árboles con **relaciones de confianza transitivas** entre dominios del mismo bosque.

*   **Un solo bosque** es lo habitual en PYMEs.
*   **Varios bosques** (empresas fusionadas, entornos de alta segregación) requieren **relaciones de confianza** entre bosques.

@section: Unidades organizativas (OU)

Las **OU** organizan objetos dentro de un dominio y son el **punto de delegación** y de **vinculación de GPO** (no confundir con contenedores “built-in” como `Users`).

**Buena práctica:** diseña OU por **función**, **ubicación** o **ciclo de vida** (ej. `OU=Equipos`, `OU=Servidores`, `OU=Desactivados`), no por departamento si eso cambia cada año sin control.

@section: Esquema y particiones

El **esquema** define **qué atributos** puede tener cada tipo de objeto (usuario, equipo, grupo).

Las particiones de directorio incluyen:

*   **Esquema** (única por bosque).
*   **Configuración** (ubicación de sitios, servidores).
*   **Dominio** (objetos por dominio).
*   **Application** (particiones opcionales para apps).

@section: Catálogo global (GC)

Los **GC** contienen un subconjunto de atributos de **todos los objetos del bosque** para acelerar búsquedas y el inicio de sesión en multidominio.

@section: DNS y AD

**AD no funciona sin DNS correcto.** Los registros **SRV** localizan servicios LDAP/Kerberos. Los DC deben apuntar a **DNS autorizados** para la zona del dominio.

@section: Caso práctico (papel)

1.  Dibuja un bosque con un dominio `aula.local` y una OU `Alumnos`.
2.  Indica qué objetos pondrías en `Alumnos` y qué GPO podrías vincular (ej. bloqueo de USB).

@quiz: ¿Qué contenedor es el límite superior de seguridad y esquema compartido en Active Directory?
@option: Unidad organizativa
@correct: Bosque
@option: Sitio de Active Directory

@quiz: ¿Qué servicio de red es obligatorio para que los clientes encuentren los controladores de dominio?
@option: DHCP
@correct: DNS con registros SRV del dominio
@option: WINS
