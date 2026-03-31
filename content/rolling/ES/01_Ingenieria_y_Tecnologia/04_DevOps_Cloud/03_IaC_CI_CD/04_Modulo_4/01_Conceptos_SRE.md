@title: Conceptos de SRE: SLO, error budget y toil
@icon: 🛡️
@description: Fiabilidad como ingeniería, indicadores, presupuesto de errores y reducción de trabajo manual.
@order: 1

# Site Reliability Engineering: fiabilidad medible

**SRE** (Site Reliability Engineering) es una disciplina que aplica principios de ingeniería de software a **operaciones**: automatizar, medir, acordar niveles de servicio y decidir cuándo parar de lanzar features si la fiabilidad está en riesgo. Esta lección introduce **SLI**, **SLO**, **SLA**, **error budget** y **toil**, sin confundirlos con marketing.

@section: SLI, SLO y SLA

* **SLI (Service Level Indicator):** métrica medible del servicio (latencia p99, disponibilidad, tasa de errores).
* **SLO (Service Level Objective):** **objetivo** interno sobre un SLI (por ejemplo «99.9% de peticiones < 300 ms en 30 días»).
* **SLA (Service Level Agreement):** **contrato** con clientes o negocio; si se incumple, suele haber consecuencias económicas o de reputación.

Los SLO deben ser **más estrictos** que el SLA para dejar margen. Si prometes demasiado en SLA sin medir bien, pagarás el precio.

@section: Error budget

El **error budget** es «cuánto puedes fallar» y seguir cumpliendo el SLO. Ejemplo: 99.9% de disponibilidad mensual permite ~43 minutos de indisponibilidad.

Si el budget se agota:

* **Congelar** lanzamientos no esenciales.
* Priorizar trabajo de **estabilidad** y reducción de deuda operativa.

Si sobra budget de forma consistente, puedes **acelerar** entrega o renegociar SLO si el coste de infra no compensa.

@section: Toil

**Toil** es trabajo manual, repetitivo y que escala linealmente con el servicio (tickets repetitivos, clics en consola). El objetivo del SRE es **automatizar** o eliminar toil para liberar tiempo a proyectos de fiabilidad (autoscaling, chaos, mejoras de observabilidad).

No todo trabajo manual es toil puntual: una migración única puede ser aceptable; el problema es el patrón recurrente.

@section: Postmortems sin culpa

Cuando hay incidente, el **postmortem** documenta:

* Línea de tiempo y impacto.
* Causa raíz (a menudo múltiples factores).
* Acciones correctivas con dueño.

Cultura **blameless**: enfocarse en sistemas y procesos, no en personas, para que la información fluya.

@section: Capacidad y fiabilidad

**N+1** redundancia, **autoscaling**, **límites de recursos** y pruebas de carga forman parte del trabajo SRE. La fiabilidad no es solo «más réplicas»: es entender **cuellos de botella** (DB, colas, dependencias externas).

@section: Relación con DevOps

**DevOps** es cultura y prácticas amplias; **SRE** es un rol/marco concreto con fuerte énfasis en **medición** y **presupuesto de errores**. Muchos equipos adoptan prácticas SRE sin titular a nadie «SRE».

@section: Errores frecuentes

* Confiar en «99.99%» sin definir cómo se mide el SLI.
* Ignorar el error budget hasta un gran incidente.
* Medir uptime del balanceador pero no la experiencia del usuario (synthetic monitoring).

@section: Laboratorio sugerido

1. Elige un servicio que uses y define **un** SLI medible (latencia o éxito de peticiones).
2. Propón un SLO mensual realista y calcula el error budget en minutos o peticiones fallidas.
3. Lista tres fuentes de toil en tu equipo y una idea de automatización para cada una.

@quiz: ¿Qué representa el error budget en la práctica SRE?
@option: El presupuesto de cloud del trimestre
@correct: La cantidad de fallos o indisponibilidad aceptable sin violar el SLO
@option: El número máximo de despliegues por día
