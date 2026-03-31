@title: JSONPath y campos: extraer datos con kubectl
@icon: 🔎
@description: Consultas -o jsonpath, rangos y depuración rápida.
@order: 4

# JSONPath: cortar el ruido de `kubectl get`

**JSONPath** es un mini-lenguaje para seleccionar campos de la respuesta JSON del apiserver. `kubectl get -o jsonpath='{...}'` es indispensable en scripts y **depuración** cuando `-o wide` no basta.

@section: Sintaxis básica

```bash
kubectl get pods -n kube-system -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
```

* `{.field}` navega el objeto.
* `[*]` itera listas.
* `range` / `end` para bucles.
* `{"\n"}` imprime saltos de línea (escapar comillas en shell).

@section: Uso típico

* Extraer **IPs** de pods (`status.podIP`).
* Listar **nodos** con labels específicos.
* Scripting de **health checks** en CI.

@section: jq como alternativa

`kubectl get -o json | jq` es más legible para pipelines complejos; JSONPath es suficiente para cortes simples sin dependencias.

@section: Errores frecuentes

* Campos `nil` → salida vacía sin error claro.
* Confundir `metadata.name` vs `metadata.generateName`.

@section: Laboratorio sugerido

1. Obtén todos los `podIP` de un Deployment con jsonpath.
2. Combina con `kubectl get node -o jsonpath` para imprimir `InternalIP`.
3. Escribe un script que falle si algún pod no tiene `Ready=True`.

@quiz: ¿Para qué sirve principalmente jsonpath en kubectl?
@option: Cifrar secretos
@correct: Extraer campos específicos de la salida JSON del apiserver para scripts y depuración
@option: Aplicar manifiestos
