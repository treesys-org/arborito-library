@title: Algorithmic Complexity: O, Ω, Θ
@icon: 📈
@description: Asymptotic notation, worst/average cases, recurrences.
@order: 1

# Complexity: predict how cost scales

**Asymptotic analysis** describes how time or memory grows with \(n\). **O** upper bound, **Ω** lower bound, **Θ** tight bound. This lesson distinguishes **worst/average/best** cases and solves common **recurrences** with the **master theorem** (when applicable).

@section: Practical rules

* Nested loops → multiply bounds.
* Halving the problem → \(O(\log n)\) levels if work per level is constant.

@section: Auxiliary space

Count recursion stacks and temporary structures.

@section: Common mistakes

* Ignoring hidden constants that matter for small \(n\).
* Confusing amortized \(O(n^2)\) with worst-case.

@section: Suggested lab

1. Implement binary search and count comparisons vs linear scan.
2. Solve \(T(n)=2T(n/2)+O(n)\).
3. Plot real timings for \(O(n\log n)\) sort vs bubble sort.

@quiz: What does O(...) typically represent in algorithms?
@option: Exact milliseconds
@correct: Asymptotic upper bound on growth of time or space
@option: Only the best case
