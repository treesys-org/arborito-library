@title: Módulos, paquetes e imports
@icon: 📂
@description: __init__.py, rutas, imports relativos.
@order: 5

# Módulos, paquetes e imports

Esta lección profundiza en **Módulos, paquetes e imports** con ejemplos ejecutables en Python 3.11+.

@section: Contexto

Python prioriza legibilidad: el código debería expresar intención sin ruido. Ese principio guía tanto scripts pequeños como servicios en producción.

@section: Ejemplo mínimo

```python
def demo() -> None:
    """Docstring útil para herramientas y humanos."""
    print("Hola desde Python")

if __name__ == "__main__":
    demo()
```

@section: Buenas prácticas

* Usa entornos virtuales (`python -m venv`) para aislar dependencias.
* Formatea con **ruff** o **black** y tipa gradualmente con **mypy** en proyectos medianos.
* Evita efectos secundarios en import time: inicializa en `main()`.

@section: Errores frecuentes

* Mutar objetos por defecto en firmas de función (`def f(x=[])`).
* Confiar en cierre de variables en lambdas dentro de bucles sin default explícito.
* Mezclar `bytes` y `str` sin decodificar explícitamente.

@section: Laboratorio

1. Crea un módulo pequeño y ejecútalo como `python -m paquete.modulo`.
2. Añade una prueba simple con `pytest` si el tema lo permite.
@quiz: ¿Qué herramienta estándar crea un entorno virtual aislado?
@option: conda obligatorio
@correct: python -m venv
@option: pip install global
