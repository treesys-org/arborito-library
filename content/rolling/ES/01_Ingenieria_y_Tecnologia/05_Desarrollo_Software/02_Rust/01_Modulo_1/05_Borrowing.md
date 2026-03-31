@title: Referencias y préstamos: & y &mut
@icon: 🔗
@description: Reglas de borrow checker, lifetimes implícitas.
@order: 5

# Referencias y préstamos: & y &mut

Puedes tener muchas referencias `&T` o una `&mut T` exclusiva. El **borrow checker** evita data races en compile time.

@quiz: ¿Cuántas referencias mutables activas a la vez a un mismo valor?
@option: Ilimitadas
@correct: Una
@option: Dos si son de solo lectura
