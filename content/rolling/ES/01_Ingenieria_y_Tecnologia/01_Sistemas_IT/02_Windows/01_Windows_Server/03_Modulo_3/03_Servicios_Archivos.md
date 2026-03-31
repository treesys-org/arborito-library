@title: Servicios de Archivos: Permisos NTFS y Shares SMB
@icon: 📁
@description: Creando carpetas compartidas seguras y eficientes.
@order: 3

# Servicios de archivos: permisos NTFS y recursos compartidos

El **servicio de archivos** en Windows Server combina **carpetas NTFS** (permisos de sistema de archivos) y **recursos compartidos SMB** (permisos de red). El **resultado efectivo** es el **más restrictivo** entre ambos.

@section: Objetivos didácticos

*   Crear un recurso compartido **SMB** y permisos coherentes.
*   Aplicar **herencia**, **denegaciones** y **RBAC** con grupos.
*   Entender **ABE** (Access Based Enumeration) y **cuotas**.

@section: Modelo de permisos

1.  **Share permissions:** quién puede alcanzar el recurso por red.
2.  **NTFS ACL:** quién puede leer/modificar archivos en disco.

**Buena práctica:** deja **share** en **Authenticated Users** o **Domain Users** con **Change** limitado según caso, y **afina en NTFS** con grupos.

**Nunca** uses `Everyone` salvo carpetas públicas controladas.

@section: Grupos y OUs

Asigna permisos a **grupos de seguridad**, no a usuarios sueltos. Documenta:

*   `GG-Departamento-RW`
*   `GG-Departamento-RO`

@section: Herencia

Si una **denegación** NTFS herede a subcarpetas, puede bloquear administradores no previstos. **Romper herencia** solo cuando necesites **excepciones** documentadas.

@section: Access Based Enumeration

Oculta carpetas a las que el usuario **no** tiene acceso, reduciendo confusión y filtraciones de nombres de proyectos.

@section: Volumen y cuotas

**File Server Resource Manager (FSRM)** permite **cuotas por usuario** y **screening** de tipos de archivo (bloquear `.exe` en carpetas de perfil).

@section: Shadow copies (VSS)

**Versiones anteriores** en cliente permite recuperar archivos borrados sin backup completo; planifica **snapshots** en horarios de baja actividad.

@section: Laboratorio

1.  Crea `\\srv-files\Datos` con `GG-Alumnos-RW` y `GG-Profesores-Full`.
2.  Verifica acceso desde cliente con usuario de prueba.
3.  Prueba **quitar** un usuario del grupo y validar que pierde acceso tras **re-login** o `klist purge`.

@quiz: ¿Cómo se calcula el permiso efectivo al combinar NTFS y permisos de recurso compartido?
@option: Se suman siempre
@correct: Se aplica el más restrictivo entre ambos
@option: Solo cuenta el permiso de recurso compartido

@quiz: ¿Qué característica oculta carpetas a las que el usuario no tiene derechos de lista?
@option: BitLocker
@correct: Access Based Enumeration (ABE)
@option: DFS
