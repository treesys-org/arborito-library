@title: Mutex and RwLock
@icon: 🔒
@description: Poisoning, deadlocks.
@order: 2

# Mutex and RwLock

Mutex can become **poisoned** after panic while locked.

@quiz: Poisoned mutex?
@option: Ignored
@correct: PoisonError on lock
@option: OS restart
