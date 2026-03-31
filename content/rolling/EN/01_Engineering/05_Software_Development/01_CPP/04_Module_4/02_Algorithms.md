@title: <algorithm> and C++20 Ranges
@icon: 🔍
@description: sort, lower_bound, ranges::views, complexities.
@order: 2

# Generic algorithms

**std::sort** is O(n log n). **lower_bound** requires a **sorted** range. **ranges** enable lazy pipeline composition.

@quiz: What precondition does lower_bound require?
@option: Empty range
@correct: Sorted order according to the comparator
@option: Unique elements
