@title: Recursos DSC y Configuraciones
@icon: 🧩
@description: Los bloques de construcción para definir el estado de tu infraestructura.
@order: 2

# Recursos DSC y configuraciones

Los **recursos** DSC son el vocabulario de tu estado deseado. Puedes usar **recursos integrados**, del **PSGallery** (xWebAdministration, SqlServerDsc), o escribir **recursos basados en clases** en PowerShell 5+.

@section: Recursos integrados

*   **File**, **Archive**, **Script**
*   **Registry**, **Service**, **WindowsFeature**
*   **User** (local)

@section: Dependencias

`DependsOn` ordena la aplicación (primero IIS, luego sitio web).

@section: Idempotencia

Aplicar dos veces la misma configuración **no** debe romper nada: si ya está en estado, **no-op**.

@section: Compilación

`Configuration` → **MOF** por nodo → **`Start-DscConfiguration -Path`** con **`-Wait -Verbose`**.

@section: Debugging

*   **`Test-DscConfiguration`** — ¿cumple?
*   Eventos en **DSC Operational** log.

@section: Práctica

1.  Crea una configuración mínima que garantice un **servicio** en **Running** (laboratorio).
2.  Ejecuta `Test-DscConfiguration` y documenta el resultado.

@quiz: ¿Qué propiedad en un recurso DSC suele indicar si el elemento debe existir o no?
@option: State
@correct: Ensure (Present/Absent)
@option: Mode

@quiz: ¿Qué cmdlet prueba si el nodo cumple la configuración sin aplicarla de nuevo?
@option: Get-DscConfiguration
@correct: Test-DscConfiguration
@option: Confirm-Dsc
