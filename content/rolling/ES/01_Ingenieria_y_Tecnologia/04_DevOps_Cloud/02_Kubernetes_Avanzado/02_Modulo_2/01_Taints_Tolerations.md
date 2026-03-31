@title: Taints y tolerations: aislar cargas y nodos dedicados
@icon: 🧲
@description: NoSchedule, PreferNoSchedule, NoExecute y matching con tolerations.
@order: 1

# Taints y tolerations: control fino de programación

Los **taints** en un nodo **repelen** pods que no tengan la **toleration** correspondiente. Úsalos para nodos dedicados (GPU, ingress), aislar cargas **spot**, o preparar nodos para **drain** gradual (`NoExecute`). Esta lección explica efectos, valores `effect` y cómo no bloquear accidentalmente todo el scheduling.

@section: Modelo mental

* **Taint en el nodo:** «solo entra quien tolere esto».
* **Toleration en el pod:** «acepto ese taint».

Sin toleration, el scheduler **no** coloca el pod en nodos con taints incompatibles (según el effect).

@section: Efectos

* **NoSchedule:** no nuevas programaciones salvo toleration; pods ya corriendo no se expulsan por el taint solo.
* **PreferNoSchedule:** el scheduler evita el nodo si puede, pero no es estricto.
* **NoExecute:** además de NoSchedule, **expulsa** pods existentes tras `tolerationSeconds` (si se especifica) que no toleran.

**Uso típico de NoExecute:** marcar nodos en mantenimiento o spot que van a desaparecer.

@section: Sintaxis

Ejemplo de taint:

```text
dedicated=gpu:NoSchedule
```

Toleration en el pod (fragmento):

```yaml
tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

**Exists** vs **Equal:** `Exists` tolera cualquier valor si la clave coincide.

@section: Taints por defecto

Los nodos **master/control-plane** suelen llevar taints para no ejecutar cargas de trabajo normales salvo toleration explícita (según versión y configuración).

@section: Interacción con affinity

**Node affinity** **atrae** pods a nodos; **taints** **repelen**. Combinan: puedes atraer a un pool GPU y aún repeler workloads sin toleration.

@section: Errores frecuentes

* Taint mal escrito en todos los nodos → cluster sin pods programables.
* Olvidar toleration en DaemonSets que deben correr en nodos tainted.
* `NoExecute` sin `tolerationSeconds` claro → pods que desaparecen de forma inesperada.

@section: Laboratorio sugerido

1. Aplica un taint `NoSchedule` a un nodo de prueba y observa que nuevos pods no se programan.
2. Añade toleration a un Deployment y verifica scheduling.
3. Prueba `NoExecute` con `tolerationSeconds` y observa reubicación.

@quiz: ¿Qué efecto de taint puede expulsar pods que ya estaban en ejecución?
@option: NoSchedule
@correct: NoExecute
@option: PreferNoSchedule
