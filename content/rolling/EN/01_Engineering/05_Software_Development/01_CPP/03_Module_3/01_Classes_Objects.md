@title: Classes and Objects in C++
@icon: 🏛️
@description: Encapsulation, const methods, this.
@order: 1

# Classes: user-defined types

A **class** groups state and behavior. **`public/private/protected`** control access. **`const`** methods promise not to mutate observable state (except `mutable`). **`this`** points to the current object.

@section: Rule of five

If you customize destructor/copy/move, review all five special members.

@quiz: What does a trailing `const` on a method mean?
@option: It throws
@correct: It does not modify observable object state (except mutable)
@option: It is static
