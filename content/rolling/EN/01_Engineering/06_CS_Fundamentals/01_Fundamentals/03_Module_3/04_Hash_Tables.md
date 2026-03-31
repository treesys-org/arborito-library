@title: Hash Tables: Functions, Collisions, and Rehashing
@icon: #️⃣
@description: Chaining vs open addressing, load factors, symbol tables.
@order: 4

# Hash tables: expected O(1) with good design

A **hash table** maps keys to values using a **hash function** and **collision resolution** (chaining, **open addressing** with linear/quadratic/double probing). **Load factor** controls **rehashing**. This lesson explains **uniformity** and **collision attacks** (hashDoS mitigated with **salting**).

@section: Functions

Murmur, xxHash; **cryptographic** hashes (SHA) for integrity, not always for tables.

@section: Open addressing

Requires **tombstones** on deletion; **robin hood hashing** reduces probe variance.

@section: Suggested lab

1. Implement a chained hash map in C.
2. Measure collisions with random vs adversarial strings.
3. Compare `unordered_map` vs `map` time for 1e6 insertions.

@quiz: What phenomenon increases probes and slows a hash table at high load?
@option: Less memory
@correct: More collisions and frequent rehashing
@option: endianness
