@title: Plantillas: funciones y clases parametrizadas
@icon: 🧩
@description: typename, SFINAE, concepts (C++20) y especialización.
@order: 4

# Templates genéricos

**template<typename T>** parametriza tipos. **SFINAE** descarta sobrecargas inválidas. **Concepts** (`std::integral`) mejoran diagnósticos en C++20. **Especialización** total/parcial para casos concretos.

@section: Instanciación

Explícita o implícita; definiciones en headers típicamente.

@section: Variadic templates

`template<typename... Args>` para empaquetar tipos.

@quiz: ¿Qué aportan los concepts en C++20 frente a SFINAE clásico?
@option: Eliminan plantillas
@correct: Mejoran errores de compilación y documentan restricciones de tipo
@option: Solo optimizan runtime
