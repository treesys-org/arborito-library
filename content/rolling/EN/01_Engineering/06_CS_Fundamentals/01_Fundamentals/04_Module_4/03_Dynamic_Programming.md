@title: Dynamic Programming: Subproblems and Memoization
@icon: 🧩
@description: Top-down vs bottom-up, optimal substructure, LCS, knapsack.
@order: 3

# Dynamic programming: never recompute twice

**DP** applies when there are **overlapping subproblems** and **optimal substructure**. **Memoization** top-down; **tabulation** bottom-up. Examples: **Fibonacci**, **LCS**, **0/1 knapsack**. This lesson teaches identifying **states** and **transitions**.

@section: Complexity

Typical \(O(\#\text{states} \times \text{transition cost})\).

@section: Space optimization

Sometimes only two rows of the table are needed.

@section: Suggested lab

1. Implement LCS with DP and compare to naive recursion.
2. Solve 0/1 knapsack for small \(n\) with bitmask + DP.
3. Derive a recurrence for counting paths in a grid with obstacles.

@quiz: When is DP useful compared to divide-and-conquer alone?
@option: Independent subproblems with no overlap
@correct: Overlapping subproblems that can be memoized
@option: Whenever input is random
