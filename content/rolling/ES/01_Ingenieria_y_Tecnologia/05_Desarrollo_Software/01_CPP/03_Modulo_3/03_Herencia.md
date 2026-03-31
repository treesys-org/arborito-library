@title: Herencia, virtual y override
@icon: 🧬
@description: Polimorfismo dinámico, tablas virtuales, slicing.
@order: 3

# Herencia y polimorfismo

**virtual** en funciones habilita **dispatch dinámico** vía **vtable**. **`override`** previene errores de firma. **Destructores virtuales** en bases polimórficas. **Slicing** ocurre si copias `Derived` en `Base` por valor.

@section: Herencia virtual

Resuelve diamantes; coste y complejidad.

@section: final

Evita más derivación o devirtualización.

@quiz: ¿Por qué el destructor de una clase base polimórfica debería ser virtual?
@option: Para ahorrar memoria
@correct: Para que al borrar vía puntero a base se destruya el objeto derivado completo
@option: Para hacerla abstracta siempre
