@title: std::thread, mutex, condition_variable
@icon: 🧵
@description: join/detach, data races, memory_order basics.
@order: 5

# Standard concurrency

**std::thread** runs callables; **join** waits. **mutex/lock_guard** protect data. **condition_variable** coordinates producers/consumers.

@section: std::jthread

C++20 joins on destruction.

@quiz: What happens if a joinable std::thread is destroyed without join/detach?
@option: Cancels
@correct: std::terminate
@option: Becomes daemon
