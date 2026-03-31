@title: Roles de Ansible: estructura y Galaxy
@icon: 🎭
@description: Estructura de roles, defaults vs vars, dependencias y ansible-galaxy.
@order: 4

# Roles: empaquetar reutilización

Un **role** es una carpeta con convención fija (`tasks`, `handlers`, `templates`, `files`, `defaults`, `vars`, `meta`). Los roles permiten compartir configuración entre playbooks y publicar en **Ansible Galaxy** o en repos privados. Esta lección cubre layout, contrato, dependencias y pruebas.

@section: Layout estándar

```
roles/nginx/
  tasks/main.yml
  handlers/main.yml
  templates/nginx.conf.j2
  files/static-site/
  defaults/main.yml
  vars/main.yml
  meta/main.yml
```

* **defaults/main.yml:** valores por defecto de **baja precedencia**; el consumidor puede sobrescribirlos fácilmente.
* **vars/main.yml:** mayor precedencia dentro del rol; úsalo para constantes internas que no deben cambiarse sin revisar el rol.
* **meta/main.yml:** dependencias de otros roles (`dependencies:`), compatibilidad de plataforma.

@section: Usar un rol en un play

```yaml
- hosts: web
  roles:
    - role: nginx
      vars:
        worker_processes: 4
```

Alternativa moderna: `import_role` / `include_role` dentro de `tasks` cuando necesitas control de orden fino con otras tasks.

@section: Ansible Galaxy y requirements

`ansible-galaxy install geerlingguy.nginx` descarga roles a `~/.ansible/roles` o ruta configurada. **Fija versiones** en `requirements.yml`:

```yaml
roles:
  - name: geerlingguy.nginx
    version: 3.1.0
```

Instala en CI con `ansible-galaxy install -r requirements.yml` antes del playbook.

@section: Contrato del rol

Documenta en README:

* Variables obligatorias y opcionales.
* Plataformas soportadas (familias, versiones).
* Side effects (reinicios, servicios habilitados).

Antipatrón: rol gigante con decenas de variables obligatorias sin defaults razonables; divide en roles compuestos.

@section: Testing con Molecule

**Molecule** (framework) prueba roles contra contenedores o VMs: converge, idempotence check, verificadores (testinfra, ansible). Integra **ansible-lint** para estilo.

Flujo típico:

1. `molecule create` — provisiona instancia de prueba.
2. `molecule converge` — aplica el rol.
3. `molecule idempotence` — segunda pasada sin cambios.
4. `molecule verify` — aserciones.
5. `molecule destroy` — limpia.

@section: Organización en monorepos

Algunos equipos guardan roles en `roles/` dentro del repo de aplicación; otros en repos dedicados versionados por tag. Elige según velocidad de cambio y número de consumidores.

@section: Errores frecuentes

* Copiar un rol de Galaxy sin leer el README y luego abrir tickets al autor por variables mal pasadas.
* Poner secretos en `defaults` en lugar de vault.
* Dependencias circulares entre roles en `meta/main.yml`.

@section: Laboratorio sugerido

1. Ejecuta `ansible-galaxy init roles/demo` y revisa la estructura generada.
2. Mueve dos tasks de un playbook monolítico a `roles/demo/tasks/main.yml` y llama al rol desde `site.yml`.
3. Añade `defaults/main.yml` con un parámetro y úsalo en un template.
4. (Opcional) Inicializa Molecule en el rol y ejecuta una converge local.

@quiz: ¿Cuál es el propósito principal de `defaults/main.yml` en un rol?
@option: Almacenar secretos siempre obligatorios
@correct: Definir valores por defecto de baja precedencia que el consumidor puede sobrescribir
@option: Listar dependencias de otros roles
