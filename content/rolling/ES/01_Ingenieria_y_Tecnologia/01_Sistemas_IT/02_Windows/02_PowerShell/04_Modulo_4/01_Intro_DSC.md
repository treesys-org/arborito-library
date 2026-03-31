@title: Intro a Desired State Configuration (DSC)
@icon: 📜
@description: Definiendo el estado deseado de tus servidores en lugar de cómo llegar a él.
@order: 1

# Introducción a Desired State Configuration (DSC)

**DSC** declara **cómo debe estar** un servidor (roles, archivos, registros) y el **Local Configuration Manager (LCM)** converge hacia ese estado. En la industria coexisten **DSC clásico** y enfoques modernos (**Azure Automanage**, **Guest Configuration**); esta lección fija conceptos de instituto.

@section: Modo declarativo

En lugar de 500 líneas imperativas “instala X, luego Y”, defines un **documento de configuración** y lo aplicas.

@section: Recursos

Cada **recurso** (WindowsFeature, File, Registry, Service…) tiene propiedades **Ensure = Present/Absent**.

@section: LCM

El **LCM** en el nodo:

*   **ApplyAndMonitor** / **ApplyAndAutoCorrect** (según modo).
*   **Pull** vs **Push** (siguientes lecciones).

@section: Ejemplo conceptual

```powershell
Configuration BaselineWeb {
  Import-DscResource -ModuleName PSDesiredStateConfiguration
  Node 'localhost' {
    WindowsFeature IIS {
      Ensure = 'Present'
      Name   = 'Web-Server'
    }
  }
}
```

Compilas a **MOF** y aplicas con **`Start-DscConfiguration`**.

@section: Límites

DSC **no sustituye** orquestación completa de aplicaciones ni parches (usa WSUS/Intune). Es excelente para **baseline** de SO.

@section: Práctica

1.  Lee `about_Desired_State_Configuration` en la ayuda.
2.  Enumera tres recursos DSC que usarías para endurecer un servidor web.

@quiz: ¿Qué componente del nodo aplica la configuración DSC en el cliente?
@option: WMI
@correct: Local Configuration Manager (LCM)
@option: DNS Client

@quiz: ¿Qué extensión tienen los archivos compilados que DSC aplica al nodo?
@option: .json
@correct: .mof
@option: .ps1
