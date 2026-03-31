@title: Variables de Ansible: precedencia y group_vars
@icon: 🔤
@description: host_vars, group_vars, extra-vars, factos y vault.
@order: 3

# Variables en Ansible: la jerarquía que evita sorpresas

Ansible combina variables de muchas fuentes: inventario, `group_vars`, `host_vars`, `vars` en el play, roles, `defaults`, `include_vars`, `set_fact` y línea de comandos. Si no entiendes la **precedencia**, debuguearás horas viendo un valor «incorrecto» en una plantilla o en un template.

@section: Orden de precedencia (simplificado)

La documentación oficial de Ansible lista muchos niveles; conceptualmente, de menor a mayor prioridad habitual:

1. `defaults` dentro de roles.
2. Variables de inventario (`host_vars`/`group_vars` en archivos).
3. `vars` definidos en `play` o `include_vars`.
4. Parámetros al invocar roles (`roles:` con `vars:`).
5. **`--extra-vars` (`-e`)** en línea de comandos — máxima prioridad habitual para overrides.

**Regla práctica:** usa `-e` para overrides puntuales en CI o pruebas; no para secretos en claro en logs públicos.

@section: Estructura de directorios típica

```
inventory/
  production/
    hosts.yml
group_vars/
  all.yml
  web.yml
host_vars/
  web1.yml
```

`group_vars/all.yml` aplica a todos; `group_vars/web.yml` solo al grupo `web`. `host_vars/web1.yml` gana sobre grupos cuando corresponde según la regla de fusión.

@section: Facts

Ansible recopila **hechos** del sistema (`ansible_facts`): IPs, distribución, mounts, interfaces. Puedes cachear facts con **fact caching** (Redis, JSON file) para acelerar grandes inventarios.

**gather_facts:** tiene coste; desactívalo (`gather_facts: false`) solo si estás seguro de no necesitar facts para la lógica del play.

**Variables de fact personalizadas:** algunos equipos usan `set_fact` para derivar valores; recuerda que `set_fact` puede marcar como «persistent» según el contexto.

@section: Vault y secretos

Variables sensibles en `group_vars/secrets.yml` cifrado con vault. En CI, inyecta la clave vía variable de entorno (`ANSIBLE_VAULT_PASSWORD_FILE`) o archivo montado de forma segura.

**Rotación:** rota secretos en el vault cuando cambia personal o tras incidente; no reutilices la misma contraseña de vault para todos los entornos si el riesgo lo exige.

@section: Depuración

* `ansible -m debug -a "var=hostvars[inventory_hostname]" -l web1`
* `ansible-playbook site.yml --diff --check` — simulación donde los módulos lo permiten (no todos los módulos soportan check mode).
* Aumenta verbosidad con `-v` a `-vvv` para ver detalles de conexión y variables resueltas (cuidado con datos sensibles en logs).

@section: Fusión de variables y hash_behaviour

Para diccionarios, `hash_behaviour` en `ansible.cfg` controla si se reemplaza o fusiona. Mal configurado, pierdes claves parciales sin notarlo.

@section: Inventario dinámico y variables

Los plugins de cloud inyectan variables por host (tags, zonas). **Documenta** qué nombres espera tu playbook para no depender de detalles de implementación del plugin.

@section: Errores frecuentes

* Crear `group_vars` con nombre de grupo que no coincide con el inventario (variables «silenciosamente» ausentes).
* Depender de `hostvars` de un host que no está en el mismo play sin `delegate_to` o sin acceso en `serial`.
* Variables en YAML con valores interpretados como booleanos (`yes`/`no` en versiones antiguas).

@section: Laboratorio sugerido

1. Define `http_port` en `group_vars/web.yml` y úsala en un template de nginx.
2. Sobrescribe con `-e http_port=8080` y observa el valor en el resultado.
3. Cifra un archivo con `ansible-vault` y ejecuta el playbook con `--ask-vault-pass`.

@quiz: ¿Qué mecanismo suele tener la mayor precedencia entre los habituales?
@option: defaults del role
@correct: Variables extra pasadas con -e / --extra-vars
@option: group_vars siempre gana a todo
