@title: unique_ptr, shared_ptr, weak_ptr
@icon: 🎈
@description: Propiedad exclusiva vs compartida, ciclos y deleters custom.
@order: 3

# Smart pointers

**unique_ptr** propiedad exclusiva; **shared_ptr** conteo atómico; **weak_ptr** evita ciclos observando pero no poseyendo. **Deleters** personalizados para `FILE*`/`close`.

@section: make_shared

Una asignación para control block + objeto.

@section: Evitar

`shared_ptr` en estructuras calientes si sincronización importa.

@quiz: ¿Qué smart pointer rompe ciclos de shared_ptr?
@option: unique_ptr
@correct: weak_ptr
@option: auto_ptr
