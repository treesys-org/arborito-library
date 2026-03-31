
@title: Examen de certificación: Python aplicado
@exam
@icon: 🐍
@description: Evalúa sintaxis, POO, ciencia de datos y APIs con Python 3.11+. Supera la mayoría de preguntas para certificar el curso.
@order: 5

# Examen final: Python aplicado

Este examen repasa los cuatro módulos del curso: fundamentos, orientación a objetos, ecosistema numérico y APIs en producción.

> **Instrucciones:** Una sola respuesta correcta por pregunta. Necesitas la mayoría de aciertos para aprobar.


## Bloque 1: Fundamentos (módulo 1)

@quiz: En Python 3, ¿qué delimita los bloques de código en lugar de llaves `{}`?
@option: Palabras clave `begin` / `end`.
@correct: Indentación consistente.
@option: Etiquetas `#region`.
@option: El carácter `:` solo, sin indentar.

@quiz: ¿Qué hace `if __name__ == "__main__":` en un script?
@option: Importa el módulo como paquete.
@correct: Ejecuta código solo cuando el archivo se lanza directamente, no al importarlo.
@option: Declara el punto de entrada para el intérprete C.
@option: Activa el modo optimizado `-O`.

@quiz: ¿Cuál es una forma idiomática de interpolar variables en una cadena en Python moderno?
@option: `"Hola " + nombre + "!"` exclusivamente.
@correct: f-strings (`f"Hola {nombre}!"`).
@option: Solo `str.format` sin alternativas.
@option: `printf` estilo C nativo.

@quiz: ¿Qué afirmación sobre listas y tuplas es correcta?
@option: Las tuplas siempre ocupan menos memoria que las listas del mismo tamaño.
@correct: Las listas son mutables; las tuplas son inmutables (el objeto tupla no cambia su contenido referenciado in-place).
@option: No pueden contener objetos de distintos tipos.
@option: Ambas solo aceptan números.

@quiz: ¿Qué peligro tiene usar una lista mutable como valor por defecto de un parámetro?
@option: Lanza `SyntaxError` al definir la función.
@correct: El mismo objeto se reutiliza entre llamadas, acumulando estado inesperado.
@option: Python lo convierte automáticamente en tupla.
@option: Solo afecta en modo interactivo.

@quiz: Un `dict` en Python 3.7+ preserva típicamente:
@option: Orden aleatorio en cada ejecución.
@correct: Orden de inserción de claves.
@option: Orden alfabético forzado de claves.
@option: Solo claves numéricas ordenadas.


## Bloque 2: POO y calidad (módulo 2)

@quiz: ¿Qué decorador suele usarse para definir un método que no recibe `self` de instancia?
@option: `@instancemethod`
@correct: `@staticmethod` (o `@classmethod` si recibe `cls`).
@option: `@abstract`
@option: `@private`

@quiz: En herencia múltiple, ¿qué mecanismo determina el orden de búsqueda de métodos?
@option: Orden alfabético de nombres de clase base.
@correct: MRO (Method Resolution Order), consultable con `ClassName.__mro__`.
@option: Solo la primera clase base listada.
@option: Aleatorio en tiempo de ejecución.

@quiz: ¿Para qué sirve principalmente un `dataclass`?
@option: Hacer la clase inmutable siempre.
@correct: Reducir boilerplate para clases que son principalmente contenedores de datos.
@option: Sustituir a Pydantic en validación runtime.
@option: Compilar la clase a extensión C.

@quiz: ¿Qué hace `raise ... from e` al encadenar excepciones?
@option: Ignora la excepción original.
@correct: Preserva el contexto (`__cause__`) para trazas más claras.
@option: Convierte la excepción en advertencia.
@option: Solo es válido dentro de `async def`.

@quiz: ¿Qué describe mejor un *decorador* en Python?
@option: Un comentario especial para el linter.
@correct: Una función callable que envuelve otra función o método para añadir comportamiento.
@option: Una anotación de tipo obligatoria.
@option: Un modificador de acceso como en Java.

@quiz: Las *type hints* en Python son principalmente:
@option: Obligatorias en tiempo de ejecución para el intérprete.
@correct: Anotaciones opcionales usadas por herramientas (mypy, IDEs) y documentación.
@option: Sustitutos de generics en tiempo de ejecución.
@option: Equivalentes a `typedef` de C con enforcement automático.


## Bloque 3: NumPy, Pandas y entorno (módulo 3)

@quiz: ¿Qué estructura de Pandas representa una columna etiquetada y homogénea?
@option: `DataFrame` siempre.
@correct: `Series`.
@option: `Panel` únicamente.
@option: `ndarray` con índice implícito.

@quiz: En NumPy, ¿qué ventaja principal aporta la vectorización sobre bucles Python puros en arrays grandes?
@option: Menor uso de memoria siempre.
@correct: Cómputo delegado a código C/optimizado, mucho más rápido.
@option: Compatibilidad obligatoria con GPU.
@option: Tipado dinámico por elemento.

@quiz: ¿Qué comando estándar crea un entorno virtual aislado en Python 3?
@option: `pip env create`
@correct: `python -m venv .venv` (o ruta deseada).
@option: `virtualenv` es el único válido hoy.
@option: `conda init` obligatorio.

@quiz: Para leer un CSV en Pandas, ¿qué función es la habitual?
@option: `pd.load_csv`
@correct: `pd.read_csv`.
@option: `pd.open_csv`
@option: `pd.import_table`

@quiz: Al hacer *scraping* ético, ¿qué práctica es esencial?
@option: Ignorar `robots.txt` si el sitio es público.
@correct: Respetar términos de uso, `robots.txt` y no saturar el servidor (rate limiting).
@option: Usar siempre hilos ilimitados.
@option: Almacenar datos personales sin base legal.

@quiz: Matplotlib suele usarse con el patrón:
@option: `plt.begin()` / `plt.commit()`
@correct: `pyplot` (por ejemplo `plot`, `show`) u orientado a objetos con `Figure` y `Axes`.
@option: Solo SVG incrustado en HTML.
@option: Requiere Jupyter obligatoriamente.


## Bloque 4: APIs, async y pruebas (módulo 4)

@quiz: En FastAPI, ¿qué papel cumple Pydantic en las rutas?
@option: Solo documentación estática.
@correct: Validación y parsing de cuerpos y parámetros en modelos tipados.
@option: Reemplaza al servidor ASGI.
@option: Gestiona pools de base de datos exclusivamente.

@quiz: ¿Qué código HTTP indica creación exitosa con recurso nuevo en REST típico?
@option: `200 OK` siempre.
@correct: `201 Created` (frecuente al crear entidades).
@option: `204 No Content` para toda escritura.
@option: `302 Found`

@quiz: En `asyncio`, ¿qué palabra clave cede el control para esperar I/O sin bloquear el hilo?
@option: `yield` en cualquier función.
@correct: `await` dentro de una función `async def`.
@option: `defer` como en Go.
@option: `wait` sin `async`.

@quiz: ¿Qué herramienta es muy habitual para pruebas unitarias en Python moderno?
@option: Solo `unittest` sin extensiones.
@correct: `pytest` (u otros runners; `unittest` sigue siendo válido).
@option: `maven test`
@option: `jest` nativo de Python.

@quiz: SQLAlchemy 2 ORM suele mapear tablas a:
@option: Diccionarios planos únicamente.
@correct: Clases Python con columnas declaradas (mapped columns) y sesiones.
@option: Solo consultas SQL crudas sin objetos.
@option: Ficheros JSON intermedios.

@quiz: Al diseñar una API REST, ¿qué método HTTP es idempotente y suele usarse para reemplazar un recurso completo?
@option: `POST`
@correct: `PUT` (en el sentido clásico de reemplazo; `PATCH` es parcial).
@option: `CONNECT`
@option: `TRACE`
