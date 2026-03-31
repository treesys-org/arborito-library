@title: Instalación, Sysprep y Post-Configuración
@icon: 💿
@description: Desde el arranque hasta la configuración inicial del servidor.
@order: 2

# Instalación, Sysprep y post-configuración

En un instituto técnico o en tu primera práctica profesional, instalar Windows Server **bien** significa: medios verificados, particiones correctas, **nombre de equipo** y **red** definidos antes de promover dominios, y plantillas **generalizadas** para clonar sin duplicar SIDs ni conflictos de seguridad.

@section: Objetivos didácticos

*   Instalar Windows Server con **Server Core** o **Desktop Experience** según necesidad.
*   Aplicar **Sysprep** para preparar imágenes clonables.
*   Completar la **configuración inicial** (red, actualizaciones, tiempo, firewall) de forma repetible.

@section: Medios y arranque

1.  Descarga la ISO oficial (evaluación o licencia por volumen).
2.  Crea un USB booteable (GPT/UEFI) o monta la ISO en una VM.
3.  Elige **idioma**, **teclado** y **edición** (Standard/Datacenter) alineada con tu licencia.

@section: Particionado recomendado

*   **Sistema:** volumen dedicado (típicamente 60–120 GB según roles y logs).
*   **Datos / VMs / archivos:** discos separados cuando sea posible; facilita backup y restauración.
*   **Resiliencia:** en producción, RAID hardware o espacios de almacenamiento según política; en laboratorio, al menos separar sistema de datos.

@section: Server Core frente a Desktop Experience

*   **Server Core:** menos superficie de ataque, menos parches de GUI, ideal para AD, DNS, DHCP, archivos, Hyper-V.
*   **Desktop Experience:** consola gráfica completa; útil si el equipo depende de herramientas administrativas locales o formación visual.

En entornos de instituto, **Core + Windows Admin Center** remoto es una combinación muy cercana a la industria.

@section: Sysprep (generalización)

`Sysprep` prepara la instalación para clonación: elimina identificadores únicos, permite que el próximo arranque ejecute el **OOBE** o mini-setup.

**Cuándo usarlo:** plantillas de VM, despliegue masivo con MDT/SCCM.

**Precaución:** no ejecutes Sysprep en un controlador de dominio en producción; promueve el DC **después** de clonar o usa despliegue automatizado según diseño.

Comando típico (referencia para laboratorio):

```text
sysprep /generalize /oobe /shutdown
```

@section: Post-configuración inicial (checklist)

1.  **Nombre de equipo:** fija un estándar (ej. `SRV-DC01`, `SRV-FILES01`).
2.  **Red estática:** IP, máscara, DNS (en dominios, **nunca** dejes el DC sin DNS apuntando a sí mismo o a otro DC).
3.  **Hora y zona:** sincronización NTP; el AD es sensible al desfase horario.
4.  **Actualizaciones:** antes de promover dominios, aplica al menos el último **cumulative update** planificado.
5.  **Firewall:** no lo desactivés “para probar”; abre reglas mínimas necesarias y documenta.

@section: Mini laboratorio guiado

1.  Instala una VM en modo **Desktop Experience** para familiarizarte con el asistente.
2.  Repite la instalación en **Core** y configura la IP con `sconfig` o PowerShell.
3.  Documenta en un informe: **capturas de particiones**, **nombre final**, **IP** y **edición** instalada.

@quiz: ¿Por qué se usa Sysprep antes de clonar una imagen de Windows Server?
@option: Para borrar el historial de navegación
@correct: Para generalizar la instalación y evitar duplicar identidades de seguridad (SID, estado de máquina)
@option: Para instalar drivers de Linux

@quiz: ¿Qué debes configurar antes de promover un servidor a controlador de dominio en un entorno de producción?
@option: Solo el fondo de pantalla
@correct: Nombre de equipo, IP estática y DNS apuntando correctamente (típicamente al futuro DC o a otro DC)
@option: Desactivar el firewall sin más
