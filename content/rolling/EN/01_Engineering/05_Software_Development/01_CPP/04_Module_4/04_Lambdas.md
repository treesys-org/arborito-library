@title: Lambdas and Captures
@icon: 🎭
@description: [=,&], mutable, constexpr lambdas.
@order: 4

# Lambdas

`[captures](params){}`; capture by value copies; by reference requires lifetime awareness. **`mutable`** allows mutating copies.

@quiz: Risk of capturing locals by reference in a lambda that outlives the function?
@option: None
@correct: Dangling reference
@option: Automatic deep copy
