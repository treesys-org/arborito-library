
@title: Certification Exam: Computer Science fundamentals
@exam
@icon: 🧮
@description: Processes, digital logic, data structures, algorithms, and compilers. Pass the majority to certify the track.
@order: 5

# Final exam: Computer science fundamentals

Four blocks aligned with the modules: systems and memory, CPU and logic, structures and complexity, classic algorithms and compilers.

> **Instructions:** Single best answer per question; majority correct to pass.


## Block 1: Processes, concurrency, I/O (module 1)

@quiz: Which structure holds kernel metadata for a process?
@option: Only the page table.
@correct: The process control block (PCB).
@option: Only the disk buffer cache.
@option: The user interrupt queue.

@quiz: What best characterizes a deadlock between threads?
@option: One thread always yields instantly.
@correct: Circular wait while holding resources and requesting others (Coffman conditions).
@option: Exactly one runnable thread.
@option: Lock-free queues only.

@quiz: What is the primary purpose of a TLB?
@option: Resolve DNS names.
@correct: Cache virtual-to-physical address translations for the MMU.
@option: Buffer network packets.
@option: Store the process tree.

@quiz: A Unix-style inode typically stores:
@option: The human-readable file name.
@correct: File metadata and block pointers; names live in directory entries.
@option: Only inline file bytes.
@option: The owner's password hash.

@quiz: Which mechanism lets devices move data to memory without per-byte CPU involvement?
@option: A tight busy-wait loop.
@correct: DMA.
@option: Only software interrupts.
@option: Pure paging with no hardware.

@quiz: A critical section in concurrent code is:
@option: The entire multithreaded program.
@correct: Code touching shared resources that must run under synchronization.
@option: Only mutex-free sections.
@option: All of user space.


## Block 2: Logic, ISA, performance (module 2)

@quiz: Which law relates NOT(A AND B) to (NOT A) OR (NOT B)?
@option: Absorption.
@correct: De Morgan.
@option: Exclusive commutativity.
@option: Double idempotence only.

@quiz: What does an ISA mainly describe?
@option: The installed operating system brand.
@correct: The instruction/register/addressing contract visible to low-level software.
@option: Only the OS scheduler.
@option: The PDF file format.

@quiz: Which pipeline hazard occurs when an instruction needs a register not yet written by the prior one?
@option: Structural hazard.
@correct: Data hazard (classic RAW).
@option: Only control hazard.
@option: L3 cache hazard.

@quiz: What does cache coherence solve on multicore CPUs?
@option: Lack of physical RAM.
@correct: Inconsistent copies of the same memory line in different caches.
@option: DNS resolution.
@option: Magnetic disk scheduling.

@quiz: Which law caps speedup when part of the work must stay sequential?
@option: Moore's law.
@correct: Amdahl's law.
@option: Only Little's law from networking.
@option: The pure Pareto principle.

@quiz: A full adder differs from a half adder because it:
@option: Only works on hexadecimal.
@correct: Accepts a carry-in while producing carry-out.
@option: Never produces carry.
@option: Requires a GPU clock.


## Block 3: Complexity and structures (module 3)

@quiz: What does big-O notation typically describe?
@option: Exact wall-clock seconds on a given CPU.
@correct: Asymptotic growth of cost versus input size (usual upper bound).
@option: Exact heap bytes.
@option: Lines of source code.

@quiz: Which operation is typically O(1) on an array with a valid index?
@option: Finding an unknown value with no ordering.
@correct: Indexed element access.
@option: Inserting at the front shifting everything.
@option: Deleting the minimum from an arbitrary heap blindly.

@quiz: Which BST traversal visits keys in sorted order for a valid tree?
@option: Preorder.
@correct: Inorder.
@option: Postorder only.
@option: Random level order.

@quiz: What worsens probing and performance when an open-address hash table load factor grows?
@option: Automatic balanced trees.
@correct: Collisions and slot clustering.
@option: Empty linked lists everywhere.
@option: LZ4 compression.

@quiz: Which graph traversal uses a queue for layer-by-layer visits?
@option: DFS with an explicit stack.
@correct: BFS.
@option: Dijkstra with zero weights only.
@option: Mandatory topological sort.

@quiz: A directed acyclic graph (DAG) naturally supports:
@option: Infinite multiplicity cycles always.
@correct: Topological ordering of dependent tasks.
@option: Guaranteed 2-colorability.
@option: Minimum spanning trees without weights.


## Block 4: Algorithms and compilers (module 4)

@quiz: What does a stable sort preserve for equal keys?
@option: Guaranteed O(1) time.
@correct: Their original relative order.
@option: Minimum external memory use.
@option: Always in-place execution.

@quiz: What is essential for classic binary search on an array?
@option: Data stored in a linked list.
@correct: Values ordered by the search key with O(1) random index access.
@option: Only prime-sized arrays.
@option: Even length only.

@quiz: When is dynamic programming preferable to naive divide and conquer?
@option: No overlapping subproblems.
@correct: Overlapping subproblems plus optimal substructure.
@option: Only dense cyclic graphs.
@option: Always sorted inputs.

@quiz: What do you typically need to prove a greedy algorithm correct?
@option: Only benchmark speed.
@correct: Greedy choice property and optimal substructure (or an exchange argument).
@option: Mandatory backtracking.
@option: At least quadratic memory.

@quiz: Which structure usually represents a program's abstract syntax in a compiler?
@option: Final machine code.
@correct: An abstract syntax tree (AST).
@option: Only the literal table.
@option: A font bitmap.

@quiz: Lexical analysis mainly emits:
@option: Linked object code.
@correct: Tokens from the source character stream.
@option: Only loop optimization plans.
@option: Deployment diagrams.
