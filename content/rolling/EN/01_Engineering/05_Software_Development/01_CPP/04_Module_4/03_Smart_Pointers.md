@title: unique_ptr, shared_ptr, weak_ptr
@icon: 🎈
@description: Exclusive vs shared ownership, cycles, custom deleters.
@order: 3

# Smart pointers

**unique_ptr** exclusive ownership; **shared_ptr** refcounted; **weak_ptr** breaks cycles. Custom **deleters** for `FILE*`/`close`.

@quiz: Which smart pointer helps break shared_ptr cycles?
@option: unique_ptr
@correct: weak_ptr
@option: auto_ptr
