@title: Playbooks y tareas: YAML, handlers y condiciones
@icon: 📋
@description: Plays, tasks, handlers, when, loops y buenas prácticas de legibilidad.
@order: 2

# Playbooks: del YAML legible a la producción

Un **playbook** es YAML que describe **qué** hacer en **qué hosts**. La legibilidad importa: en incidentes leerás esto bajo estrés; nombres de tarea claros y estructura consistente reducen errores. Esta lección cubre plays, tasks, handlers, condiciones, bucles y trampas habituales.

@section: Anatomía de un play

```yaml
- name: Configurar web tier
  hosts: web
  become: true
  vars:
    app_version: "2.3.1"
  tasks:
    - name: Instalar nginx
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: true
```

Cada **task** invoca un **módulo** con argumentos. El campo `name` es **documentación**; descríbelo como acción de negocio («Instalar nginx») no como módulo crudo.

**Serialización:** `serial: "25%"` o número fijo para rolling updates (por ejemplo reinicios de cluster).

@section: Handlers

Los **handlers** son tasks especiales que se ejecutan **al final del play** si fueron **notificados** por un cambio (`notify`). Patrón típico: reiniciar un servicio solo si la configuración cambió.

```yaml
- name: Plantear nginx.conf
  ansible.builtin.template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Reiniciar nginx

handlers:
  - name: Reiniciar nginx
    ansible.builtin.service:
      name: nginx
      state: restarted
```

**Nota:** si varias tasks notifican el mismo handler, se ejecuta **una vez** al final (salvo `force_handlers` o errores intermedios). Si necesitas orden estricto entre handlers, usa una cadena de handlers o una task normal con `when`.

@section: Condiciones y expresiones

* `when: ansible_os_family == "Debian"` — ramas por SO o por facts.
* `failed_when` — redefine cuándo una task cuenta como fallo (útil con `command` que devuelve códigos ambiguos).
* `changed_when` — ajusta la semántica de `changed` para scripts que siempre imprimen algo.

Combina con `ansible_facts` recopilados al inicio del play (`gather_facts: true` por defecto).

@section: Bucles y listas

* `loop:` con lista o diccionario.
* `loop_control:` para `label` y límites de concurrencia en algunos contextos.

Evita `with_items` en playbooks nuevos si tu versión de Ansible ya prefiere `loop` (consulta la documentación de tu versión).

@section: Jinja2 en templates

Variables de host (`hostvars`), grupo (`group_vars`), vault y `vars` del play se inyectan en plantillas `.j2`. Mantén la lógica **simple** en Jinja; si crece, usa `set_fact` con moderación o filtros dedicados.

**Diferencias:** `ansible-playbook --diff` muestra cambios en archivos para revisar antes de aplicar en entornos delicados.

@section: Ansible Vault

Secretos en Git deben ir cifrados con `ansible-vault encrypt` o `encrypt_string`. En CI, inyecta la contraseña o archivo de clave vía variable de entorno o mecanismo seguro del pipeline. **Nunca** pegues secretos en claro en repositorios públicos.

@section: Roles y `import_tasks` vs `include_tasks`

`import_tasks` se resuelve al cargar el playbook; `include_tasks` es dinámico en runtime. Para la mayoría de casos de organización, los **roles** son la forma canónica de estructurar (ver lección de roles).

@section: Errores frecuentes

* Indentación YAML incorrecta (espacios, no tabs).
* Olvidar `become` en tareas que requieren root.
* Handlers que no se disparan porque la task no reportó `changed` (revisa módulos y `changed_when`).
* Uso de `shell` con pipes sin comillas y sorpresas de escape.

@section: Laboratorio sugerido

1. Escribe un playbook que instale `nginx` y despliegue un `index.html` con `template`.
2. Añade un handler de reinicio de `nginx` y verifica que solo corre cuando cambia la plantilla (ejecuta dos veces seguidas).
3. Añade una task condicional `when` que instale un paquete solo en Debian/Ubuntu.

@quiz: ¿Cuándo se ejecutan normalmente los handlers en Ansible?
@option: Antes de cada task
@correct: Al final del play, si fueron notificados por una task que cambió el sistema
@option: Nunca, son solo documentación
