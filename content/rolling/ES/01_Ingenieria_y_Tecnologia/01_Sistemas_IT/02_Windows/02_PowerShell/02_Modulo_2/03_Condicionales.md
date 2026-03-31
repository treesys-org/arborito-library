@title: Sentencias Condicionales (If, Switch)
@icon: 🧭
@description: Tomando decisiones dentro de tus scripts.
@order: 3

# Condicionales: if y switch

**`if`** evalúa expresiones booleanas. **`switch`** puede reemplazar cadenas de `if/elseif` y admite **regex** y **wildcard** con `-Regex` / `-Wildcard`.

@section: if / elseif / else

```powershell
$svc = Get-Service bits
if ($svc.Status -eq 'Running') {
  'OK'
} elseif ($svc.Status -eq 'Stopped') {
  'Parado'
} else {
  $svc.Status
}
```

@section: switch

```powershell
$rol = 'FILE'
switch ($rol) {
  'DC'   { 'Dominio' }
  'FILE' { 'Archivos' }
  default { 'Otro' }
}
```

### switch -file

Procesa líneas de un archivo; útil para logs simples.

@section: Operadores de comparación de cadenas

`-ceq` case-sensitive; `-eq` es case-insensitive por defecto.

@section: Try a futuro

Para errores **terminantes**, combina con **`try/catch`** (siguiente lección).

@section: Práctica

1.  Escribe un script que lea el nombre de un servicio y **inicie** solo si está parado.
2.  Usa `switch` con tres códigos de salida simulados.

@quiz: ¿Qué palabra clave define la rama por defecto en switch?
@option: else
@correct: default
@option: catch

@quiz: ¿Son las comparaciones de cadenas con -eq sensibles a mayúsculas por defecto?
@option: Sí
@correct: No (usa -ceq para distinguir mayúsculas)
@option: Solo en Linux
