@title: Pila vs montículo y tiempo de vida
@icon: 🗂️
@description: Objetos locales, estáticos, thread_local y alineación.
@order: 5

# Storage duration

**Automático** (stack): destrucción al salir del bloque. **Dinámico** (heap): hasta `delete`. **Estático** y **thread_local** viven según su región.

@section: Stack overflow

Recursión infinita o `alloca` grande.

@section: SSO

`std::string` puede almacenar corto en buffer interno sin heap.

@quiz: ¿Dónde viven típicamente variables locales `int x` de una función?
@option: Heap
@correct: En el marco de pila (stack frame) de la función
@option: En la sección .bss siempre
