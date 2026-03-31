@title: Seguridad de Ejecución y Firmas
@icon: 🛡️
@description: Entendiendo las políticas de ejecución para ejecutar scripts de forma segura.
@order: 5

# Políticas de ejecución y scripts firmados

La **execution policy** no es un sistema de seguridad perfecto, pero es una **primera barrera** frente a scripts maliciosos. En empresas se combina con **Constrained Language**, **AppLocker** y **firmas** de código.

@section: Objetivos didácticos

*   Leer y establecer **ExecutionPolicy** (ámbitos: Machine, User, Process).
*   Firmar scripts con **certificado de código** (conceptualmente).
*   Entender **bypass** puntual vs política corporativa.

@section: Ver política

```powershell
Get-ExecutionPolicy -List
```

Ámbitos: `MachinePolicy`, `UserPolicy`, `Process`, `CurrentUser`, `LocalMachine` (precedencia de arriba abajo).

@section: Valores habituales

*   **Restricted:** solo consola interactiva.
*   **RemoteSigned:** scripts locales sin firmar OK; descargados deben estar firmados.
*   **AllSigned:** todo debe estar firmado.
*   **Unrestricted** / **Bypass:** peligroso salvo automatización controlada.

**Nota:** en servidores, la política real puede venir de **GPO**.

@section: Firmar script (visión general)

1.  Obtén un certificado de **Code Signing** (PKI interna o comercial).
2.  `Set-AuthenticodeSignature -FilePath .\script.ps1 -Certificate $cert`
3.  Distribuye la **CA raíz** de confianza a clientes.

@section: Unblock

Archivos descargados llevan **Zone.Identifier** (ADS). `Unblock-File` quita la marca “de Internet”:

```powershell
Unblock-File .\script.ps1
```

No es “arreglar seguridad”, es reconocer que confías en el origen.

@section: Buenas prácticas

*   Repositorio Git interno + revisión de código.
*   **Least privilege** para cuentas de servicio.
*   **Logging** con **Script Block Logging** (avanzado, vía política).

@section: Práctica

1.  Comprueba la política efectiva en tu laboratorio.
2.  Explica en dos frases por qué **RemoteSigned** es un compromiso habitual en desarrollo.

@quiz: ¿Qué cmdlet muestra la política de ejecución en todos los ámbitos aplicables?
@option: Get-Policy
@correct: Get-ExecutionPolicy -List
@option: Show-ExecutionPolicy

@quiz: ¿Para qué sirve Set-AuthenticodeSignature?
@option: Cifrar el disco
@correct: Firmar digitalmente un script para integridad y origen
@option: Comprimir scripts
