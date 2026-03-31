@title: Clases y objetos en C++
@icon: 🏛️
@description: Encapsulación, miembros, const-correctness y this.
@order: 1

# Clases: tipos definidos por el usuario

Una **clase** agrupa estado (**miembros**) y comportamiento (**métodos**). **`public/private/protected`** controlan acceso. **`const`** en métodos promete no mutar el objeto (excepto `mutable`). El puntero **`this`** referencia la instancia actual.

@section: Definición típica

```cpp
class Counter {
  int value_{0};
public:
  void inc() { ++value_; }
  int get() const { return value_; }
};
```

**class** vs **struct**: por defecto `public` en struct.

@section: Regla de cinco

Si defines destructor copia/move personalizados, revisa las cinco operaciones.

@quiz: ¿Qué indica un método marcado const al final?
@option: Que lanza excepciones
@correct: Que no modifica el estado observable del objeto (salvo mutable)
@option: Que es estático
