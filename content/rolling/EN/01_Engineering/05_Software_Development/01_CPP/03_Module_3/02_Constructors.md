@title: Constructors, Delegation, Initialization
@icon: 🧱
@description: Member init lists, =default/delete, NRVO.
@order: 2

# Safe, efficient construction

Use **member initializer lists** to construct members directly. **`=default`/`=delete`** control special members. **RVO/NRVO** can elide copies for returned locals.

@section: Uniform initialization

`{}` helps prevent narrowing in many cases.

@quiz: Why prefer initializer lists over assigning in the constructor body?
@option: Legally required always
@correct: Constructs members directly and avoids extra work/invalid states
@option: The body cannot exist
