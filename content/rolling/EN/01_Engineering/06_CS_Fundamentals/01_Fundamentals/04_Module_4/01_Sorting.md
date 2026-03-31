@title: Sorting: MergeSort, QuickSort, and Limits
@icon: 🔃
@description: Stability, comparison Ω(n log n), counting/radix sorts.
@order: 1

# Sorting algorithms: theory and practice

General **comparison** sorting has an **Ω(n log n)** worst-case lower bound. **MergeSort** is \(O(n\log n)\) stable but needs \(O(n)\) extra space; **QuickSort** averages \(O(n\log n)\), worst \(O(n^2)\) with bad pivots. **Counting/Radix** break the bound when keys are bounded integers. This lesson covers **stability** and **in-place** behavior.

@section: HeapSort

\(O(n\log n)\) in-place, not stable.

@section: std::sort

libstdc++ uses **introsort** hybrid.

@section: Suggested lab

1. Implement merge sort and compare to `qsort`.
2. Build an adversarial case for Lomuto quicksort.
3. Sort large integers with radix base 256.

@quiz: What does a stable sort preserve for equal keys?
@option: Clock speed
@correct: The original relative order among elements with equal keys
@option: Zero memory usage
