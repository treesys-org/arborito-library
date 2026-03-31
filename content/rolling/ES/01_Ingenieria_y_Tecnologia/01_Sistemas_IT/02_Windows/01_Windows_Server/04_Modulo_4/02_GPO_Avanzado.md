@title: GPO: Preferencias vs Políticas y Filtrado WMI
@icon: ⚙️
@description: Técnicas avanzadas para aplicar configuraciones de forma granular.
@order: 2

# GPO avanzado: preferencias, WMI y seguridad

Más allá de las políticas **administrativas clásicas**, las **Group Policy Preferences (GPP)** permiten tareas de configuración detallada (mapas de unidad, registros, tareas programadas) con **elementos de nivelación** y **acciones** (crear, reemplazar, actualizar, eliminar).

@section: Objetivos didácticos

*   Diferenciar **Policy** vs **Preference**.
*   Crear un **filtro WMI** para aplicar una GPO solo a ciertos modelos de hardware.
*   Usar **item-level targeting** en preferencias.

@section: Políticas vs preferencias

| Enfoque | Comportamiento típico |
| :--- | :--- |
| **Policies** | A menudo “tatúan” el registro en ramas protegidas; el usuario no puede revertir fácilmente |
| **Preferences** | Aplica valores pero puede ser sobrescrito si no marcas “remove this item when no applied” |

**Regla:** preferencias para **mapear unidades** por grupo de seguridad; políticas endurecidas para **seguridad** (contraseñas, firewall).

@section: Filtros WMI

Un **filtro WMI** es una consulta **WQL** que determina si la GPO aplica al equipo.

Ejemplo conceptual: aplicar drivers solo si el modelo es `Latitude 5540`:

```text
SELECT * FROM Win32_ComputerSystem WHERE Model = 'Latitude 5540'
```

**Importante:** los filtros WMI añaden **latencia** al procesamiento de directivas; úsalos con criterios eficientes y prueba en laboratorio.

@section: Item-level targeting (GPP)

En preferencias, cada elemento puede tener **condiciones** (IP range, grupo, variable de entorno). Es más flexible que separar OUs para cada caso.

@section: Loopback de directiva

**Loopback** permite aplicar **configuración de usuario** basada en **dónde está el equipo** (OU del PC), no solo en la OU del usuario. Útil en aulas y quioscos.

Modos:

*   **Merge:** combina GPO de usuario con las del equipo.
*   **Replace:** las GPO de usuario del equipo sustituyen a las del usuario.

@section: Orden y troubleshooting

1.  `gpresult /h` en cliente.
2.  Revisa **Link order** en GPMC.
3.  Comprueba **DNS** y conectividad al **SYSVOL** (`\\dominio\SYSVOL`).

@section: Práctica

1.  Crea un filtro WMI simple que compruebe `TotalPhysicalMemory`.
2.  Documenta el tiempo de inicio de sesión **antes** y **después** (evita WMI pesado en cada logon).

@quiz: ¿Para qué sirve principalmente el modo Loopback de directiva de grupo?
@option: Para acelerar Internet
@correct: Para aplicar configuración de usuario según la ubicación del equipo en AD (p. ej. aulas)
@option: Para desactivar el firewall

@quiz: ¿Qué lenguaje de consulta usan los filtros WMI en GPO?
@option: SQL estándar ANSI
@correct: WQL (WMI Query Language)
@option: PowerShell únicamente
