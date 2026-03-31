@title: Inheritance, virtual, override
@icon: 🧬
@description: Dynamic polymorphism, vtables, slicing.
@order: 3

# Inheritance and polymorphism

**virtual** enables dynamic dispatch via **vtable**. **`override`** catches signature mistakes. **Virtual destructors** for polymorphic bases. **Slicing** happens if you copy `Derived` into `Base` by value.

@quiz: Why should a polymorphic base class destructor be virtual?
@option: Save memory
@correct: Deleting via base pointer destroys the complete derived object
@option: Makes it abstract
