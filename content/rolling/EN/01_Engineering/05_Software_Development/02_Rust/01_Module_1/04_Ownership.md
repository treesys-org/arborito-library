@title: Ownership rules
@icon: 🧩
@description: Move, Copy, Drop, RAII.
@order: 4

# Ownership rules

Each value has one owner; dropping calls **Drop**. **Move** invalidates the source unless `Copy`.

@quiz: What happens assigning String without clone?
@option: Auto deep copy
@correct: Move; original invalidated
@option: Nightly only
