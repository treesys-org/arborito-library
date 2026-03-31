@title: Function Pointers and std::function
@icon: 📞
@description: Callbacks, templates, type-erasure costs.
@order: 4

# Higher-order functions in C++

C-style function pointers work for C callbacks. **`std::function`** type-erases callables; can add overhead. For hot paths, templates and lambdas avoid indirection.

@section: Prefer lambdas over std::bind

@quiz: What is usually more readable than std::bind for partial application?
@option: Macros
@correct: Lambdas
@option: goto
