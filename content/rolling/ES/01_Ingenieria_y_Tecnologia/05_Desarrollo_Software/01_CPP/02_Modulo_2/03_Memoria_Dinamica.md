@title: new/delete vs make_unique/make_shared
@icon: 🧱
@description: RAII, excepciones, arrays y reglas de tres/cinco.
@order: 3

# Memoria dinámica y RAII

**new/delete** son bajonivel; prefiere **smart pointers** y contenedores. **`std::make_unique`** evita fugas ante excepciones. **`std::vector`** gestiona arrays dinámicos. Esta lección introduce **RAII**: el destructor libera recursos.

@section: Regla de cero

No escribas destructores manuales si puedes componer tipos RAII.

@section: Arrays

`new T[n]` requiere `delete[]`.

@quiz: ¿Qué plantilla crea un unique_ptr sin exponer new crudo de forma idiomática?
@option: allocate_shared
@correct: make_unique
@option: enable_shared_from_this
