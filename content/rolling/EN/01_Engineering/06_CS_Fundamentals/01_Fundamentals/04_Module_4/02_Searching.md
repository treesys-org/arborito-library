@title: Searching: Binary, Interpolation, and Strings
@icon: 🔍
@description: Lower bound, endpoints, KMP/rolling hash intro.
@order: 2

# Searching: shrink the answer space

**Binary search** on a sorted array uses \(O(\log n)\) comparisons; **lower_bound** finds the first element ≥ value. Generalizes to **answer binary search** on monotonic predicates. **KMP** matches patterns; **rolling hash** for substrings. Mentions **ternary search** on unimodal functions.

@section: Pitfalls

**Off-by-one** errors; infinite loops if `mid` computed incorrectly.

@section: Suggested lab

1. Implement `lower_bound` manually.
2. Solve a “minimum time to finish K jobs” problem with answer binary search.
3. Implement KMP for a fixed pattern.

@quiz: What is essential for classic binary search on an array?
@option: Even length
@correct: Being sorted (under the comparison predicate)
@option: Unique elements
