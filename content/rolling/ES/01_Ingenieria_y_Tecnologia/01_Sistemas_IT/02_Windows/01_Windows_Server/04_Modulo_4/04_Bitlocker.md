@title: BitLocker y Seguridad Física
@icon: 🛡️
@description: Cifrando los discos duros para proteger los datos en reposo.
@order: 4

# BitLocker: cifrado de volumen y protección física

**BitLocker** cifra volúmenes NTFS/ReFS para que, si roban un portátil o desmontan un disco, los datos **no** se lean sin la clave correcta. En políticas de empresa y normativas (RGPD, sector salud), el cifrado en reposo es requisito frecuente.

@section: Objetivos didácticos

*   Habilitar BitLocker con **TPM** (y PIN opcional).
*   Guardar **clave de recuperación** en AD (MBAM o extensión de esquema según diseño).
*   Explicar el arranque seguro **UEFI** y **Secure Boot** en el contexto de BitLocker.

@section: Requisitos

*   **TPM 1.2/2.0** en hardware (o unidad USB de arranque en escenarios legacy).
*   **Partición de sistema** correcta (reservada para arranque).
*   **BIOS/UEFI** actualizado.

@section: Modos de autenticación

*   **TPM only:** transparente para el usuario tras el arranque (riesgo si el atacante arranca el SO mientras está desbloqueado).
*   **TPM + PIN:** el usuario introduce PIN antes de cargar Windows (más seguro).
*   **TPM + llave USB:** menos habitual en oficina.

@section: Recuperación

Genera y **almacena** la **recovery password** (48 dígitos):

*   En **AD** (requiere extensión de esquema y GPO para backup automático).
*   En **Azure AD** para dispositivos unidos a la nube.

Sin clave de recuperación, un disco dañado puede significar **pérdida de datos**.

@section: BitLocker To Go

Cifrado de **unidades extraíbles** USB con contraseña o smart card.

@section: Rendimiento

En CPUs modernas con **AES-NI**, el impacto es bajo. Aun así, en servidores con I/O intensivo, prueba **benchmarks** antes y después.

@section: Directiva de grupo

GPO típicas:

*   **Require additional authentication at startup** (PIN).
*   **Choose drive encryption method** (AES-128/256).
*   Forzar BitLocker en **móviles** corporativos.

@section: Práctica (laboratorio)

1.  Activa BitLocker en una VM (puede requerir TPM virtual o política sin TPM solo para pruebas).
2.  Exporta la **recovery key** a un archivo y guárdala en lugar seguro simulado.
3.  Documenta el procedimiento si un usuario **olvida** el PIN.

@quiz: ¿Qué componente hardware suele usarse para almacenar claves de BitLocker de forma segura?
@option: Tarjeta gráfica
@correct: TPM (Trusted Platform Module)
@option: Módem 4G

@quiz: ¿Qué sucede si pierdes la contraseña de recuperación de BitLocker y el volumen está dañado?
@option: Windows se reinstala solo
@correct: Puedes perder el acceso a los datos de forma permanente
@option: Microsoft te envía la clave por correo
