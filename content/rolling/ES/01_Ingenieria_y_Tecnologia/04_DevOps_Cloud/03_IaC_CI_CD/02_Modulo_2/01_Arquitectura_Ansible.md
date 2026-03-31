@title: Arquitectura de Ansible: inventario, playbooks y conexión
@icon: 🎭
@description: Inventario, plugins, estrategia de ejecución y diferencias con agentes permanentes.
@order: 1

# Ansible: arquitectura sin agente persistente

Ansible automatiza configuración, despliegue de aplicaciones y orquestación **sin instalar un agente** en los nodos gestionados: usa SSH en Linux y WinRM en Windows. El **control node** (tu portátil, un bastión o un runner de CI) ejecuta `ansible-playbook` y empuja módulos Python o PowerShell sobre la conexión. Esta lección fija el mapa mental: inventario, transporte, privilegios y límites del modelo.

@section: Componentes principales

* **Inventario:** lista de hosts y grupos (`inventory.ini`, YAML estático o fuente dinámica desde cloud).
* **Playbook:** lista ordenada de **plays**; cada play aplica **tasks** a un patrón de hosts (`hosts:`).
* **Módulos:** unidades idempotentes (`apt`, `copy`, `systemd`, `k8s`, `win_regedit`, …).
* **Plugins:** conexión (SSH, paramiko), inventario (AWS EC2, Azure RM), callback (formateo de salida), filtros Jinja2, lookup (vault, archivos).

**Ejecución:** por defecto el orden es **lineal** por host (tarea 1 en todos los hosts, luego tarea 2…), configurable con estrategias.

@section: Inventario estático

Ejemplo mínimo INI:

```ini
[web]
web1.example.com ansible_user=deploy
web2.example.com ansible_user=deploy

[db]
db1.example.com

[web:vars]
http_port=80
```

Equivalente en YAML: lista de hosts con `hosts:` y `children` para jerarquías. Los grupos permiten aplicar variables comunes y limitar ejecución con `-l web`.

@section: Inventario dinámico

Plugins de inventario consultan APIs (EC2, GCP, Azure) y generan hosts y variables al vuelo. Útil cuando la flota escala con autoscaler o etiquetas cambian constantemente. Configuras el plugin en `ansible.cfg` o pasas `-i` apuntando a un script/plugin.

**Buenas prácticas:** etiqueta instancias de forma consistente (`Role=app`, `Env=prod`); limita el inventario con filtros para no ejecutar contra «toda la cuenta».

@section: Conexión y privilegios

* **Linux:** SSH con clave o contraseña; `ansible_user`, `ansible_ssh_private_key_file`, `ansible_port` por host o grupo.
* **Elevación:** `become: true` + `become_method sudo` para tareas que requieren root; `become_user` para impersonar otro usuario.
* **Windows:** `ansible_connection: winrm`, credenciales seguras, certificados TLS; muchos equipos usan dominios y Kerberos en entornos corporativos.

**Timeouts:** `timeout` en `ansible.cfg` o por tarea para redes lentas; `async`/`poll` para operaciones largas.

@section: Estrategia de ejecución

* **linear:** por defecto; sincroniza el avance por tarea (predecible para depuración).
* **free:** cada host avanza lo más rápido posible (puede alterar orden relativo entre hosts).
* **mitogen** (externo) y otras optimizaciones para miles de nodos.

Elige según consistencia vs. velocidad; para parches críticos, **linear** suele ser más fácil de razonar.

@section: Idempotencia en Ansible

Los módulos deben ser **idempotentes**: si el estado ya coincide, reportan `ok` en lugar de `changed`. Si ves `changed` en cada ejecución, revisa condiciones, uso de `command`/`shell` sin `creates`, o módulos que no soportan bien el estado actual.

**Módulos crudos:** `command` y `shell` son potentes pero peligrosos; encapsula en scripts con checks o usa módulos dedicados cuando existan.

@section: Cuándo Ansible encaja mejor

* Servidores con SSH persistente y ciclo de vida mediano.
* Configuración heterogénea (Linux + Windows + appliances con API).
* Patrones de rolling update con `serial:` en el play.

**Cuándo es incómodo:** contenedores efímeros sin SSH (mejor imagen inmutable); o redes donde solo hay API de hardware propietaria (a veces Terraform + API + scripts).

@section: Seguridad básica

* **Ansible Vault** para secretos en el repo (cifrados).
* Limita quién puede ejecutar playbooks contra producción (AWX/Tower, roles, aprobaciones).
* No dejes claves privadas en el repositorio de aplicación; usa vault o secret managers integrados.

@section: Laboratorio sugerido

1. Instala Ansible en un control node (paquete del SO o `pip` en venv).
2. Crea dos VMs o contenedores con SSH y un usuario con sudo.
3. Define `inventory` con ambos hosts y ejecuta `ansible -m ping all`.
4. Escribe un playbook de una tarea que instale un paquete idempotente (`apt` o `dnf`) y ejecútalo con `ansible-playbook -i inventory site.yml`.

@section: Errores frecuentes

* Inventario que apunta a `localhost` por error y «funciona» solo en desarrollo.
* Falta de `become` en tareas que requieren privilegios.
* Mezclar Python 2/3 en el control node sin `ansible_python_interpreter` en hosts exóticos.

@quiz: ¿Qué afirmación describe mejor a Ansible frente a agentes permanentes?
@option: Requiere un daemon instalado en cada servidor
@correct: Se apoya en SSH/WinRM y no necesita agente permanente en los nodos gestionados
@option: Solo funciona en Windows
