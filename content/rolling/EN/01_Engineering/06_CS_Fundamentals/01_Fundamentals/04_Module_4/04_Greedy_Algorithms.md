@title: Greedy Algorithms: Locally Optimal Choices
@icon: 🍯
@description: Greedy property, interval scheduling, fractional knapsack.
@order: 4

# Greedy: fast when exchange arguments work

A **greedy** algorithm picks the locally best option each step. It is only correct if there is **greedy choice property** and **optimal substructure** (exchange argument). Examples: **interval scheduling**, **fractional knapsack**, **Huffman** (with the right data structures). Contrast with DP where greedy fails.

@section: Counterexamples

**0/1 knapsack** is not solved by value/weight greedy in general.

@section: Suggested lab

1. Prove interval scheduling by finish time.
2. Implement Huffman for given frequencies.
3. Find coin cases where greedy fails if denominations are not canonical.

@quiz: What do you typically need to prove a greedy is correct?
@option: It is O(n log n)
@correct: An exchange argument or greedy-choice property supporting the local decision
@option: It uses a priority queue
