@title: struct, union y alignas
@icon: 🧱
@description: Agregados, POD, padding y alineación.
@order: 5

# Tipos agregados y layout de memoria

**struct** agrupa miembros; el compilador inserta **padding** para alineación. **`alignas`** fuerza alineación. ** designated initializers** (C++20) simplifican construcción. **union** comparte almacenamiento — útil para interpretaciones, peligroso sin disciplina.

@section: offsetof

`offsetof` solo para tipos estándar; cuidado con C++ y layout.

@section: Laboratorio

1. Imprime `sizeof` y offsets de campos en un struct con reordenamiento.
2. Usa `static_assert(std::is_standard_layout_v<T>)` cuando importe.

@quiz: ¿Por qué dos structs con mismos miembros pueden tener distinto sizeof?
@option: Porque C++ lo prohíbe
@correct: Porque el orden y el padding cambian el layout en memoria
@option: Solo por el nombre del struct
