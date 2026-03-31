@title: Grafana: dashboards, datasources y alertas
@icon: 📊
@description: Visualización, variables, provisioning y alertas unificadas.
@order: 3

# Grafana: visualizar y alertar sobre métricas y logs

**Grafana** es una plataforma de observabilidad que consulta **datasources** (Prometheus, Loki, Elasticsearch, Tempo, cloud vendors) y presenta **dashboards** interactivos. También puede **alertar** (Unified Alerting) integrando con canales de notificación. Esta lección cubre conceptos, variables, provisioning y buenas prácticas.

@section: Datasources y paneles

Un **datasource** apunta a una URL de Prometheus, Loki, etc., con autenticación. Un **dashboard** contiene **paneles** (time series, tablas, heatmaps, stat).

Cada panel ejecuta una query (PromQL, LogQL…) con **intervalo** y **step** que debes ajustar al rango temporal para rendimiento.

@section: Variables de dashboard

**Variables** (`$cluster`, `$namespace`) permiten un mismo dashboard para muchos entornos. Fuentes: query a Prometheus, custom, datasource.

**Variables en cascada** reducen cardinalidad en la UI y evitan copiar dashboards por entorno.

@section: Provisioning

En GitOps, versiona dashboards como JSON/YAML bajo `provisioning/dashboards` o usa **Grafana Operator** en Kubernetes. Así el dashboard es **código** revisado en PR.

**UIDs estables** evitan duplicados al importar.

@section: Alerting en Grafana

**Unified Alerting** define reglas sobre queries de paneles o alertas independientes. Integra con **Alertmanager** o canales directos.

Ventaja: una sola UI para métricas y logs si usas Loki. Riesgo: duplicar reglas ya en Prometheus; **documenta** la fuente de verdad de cada alerta.

@section: Organización y permisos

* **Folders** por equipo o dominio.
* **Roles** (viewer, editor, admin) vía SSO (OAuth, SAML).

En entornos multi-tenant, separa instancias o organizaciones si el aislamiento lo exige.

@section: Buenas prácticas de dashboard

* Paneles con **título y descripción** que expliquen qué hacer si la curva se rompe.
* **Unidades** correctas (s, ms, percent, bytes).
* Evita decenas de paneles en un solo dashboard: divide por historia de troubleshooting.

@section: Plugins

Plugins oficiales y de comunidad amplían datasources y visualizaciones. **Pin** versiones en despliegues productivos y revisa licencias.

@section: Errores frecuentes

* Queries que escanean años de datos con step de 1s (navegador y backend saturados).
* Dashboards sin variables que multiplican copias «prod», «staging», …
* Credenciales de datasource en la UI sin backup ni auditoría.

@section: Laboratorio sugerido

1. Instala Grafana localmente o usa la imagen oficial.
2. Añade Prometheus como datasource y crea un panel con `up`.
3. Crea una variable `job` poblada desde Prometheus y úsala en una query.
4. Exporta el dashboard a JSON y guárdalo en un repo de prueba.

@quiz: ¿Para qué sirven principalmente las variables de dashboard en Grafana?
@option: Para eliminar la necesidad de datasources
@correct: Parametrizar consultas y reutilizar el mismo dashboard entre entornos o servicios
@option: Solo cambiar el tema claro/oscuro
