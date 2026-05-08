+++
order = 7
subject = "Computer Science"
tags = ["cs", "algorithms", "sorting", "quicksort", "mergesort", "heapsort", "radix-sort", "lower-bound"]
+++

# Algorithms — Sorting Algorithms

## 7.1 Why Sorting Matters

Q: Why is SORTING one of the most-studied problems in computer science?
A: Because (1) it's ubiquitous — search, database indexes, statistics, rendering all depend on sorted data. (2) It's RICH: many algorithms with distinct trade-offs (time, space, stability, adaptivity). (3) Sorting is a CANONICAL teaching vehicle for divide-and-conquer, randomization, amortization, and lower bounds. (4) $\Theta(n \log n)$ is a sharp OPTIMAL bound for comparison-based sorting — a foundational result.

## 7.1b Pattern Recognition: Picking the Right Sort

Q: You need to sort $n < 50$ elements, possibly nearly sorted. Which sort?
A: Insertion sort — $O(n)$ best case on nearly-sorted, low constants, simple, stable.

Q: You need a guaranteed $O(n \log n)$ worst case AND stability. Which sort?
A: Mergesort — both properties out of the box. Cost: $O(n)$ extra space.

Q: You need fast average performance and in-place sorting; stability optional. Which sort?
A: Quicksort with randomized pivot (or introsort if you need a $O(n \log n)$ worst-case ceiling).

Q: All keys are integers in a known small range $[0, k]$ with $k = O(n)$. Which sort?
A: Counting sort — $O(n + k)$, beats $\Omega(n \log n)$ comparison lower bound by exploiting the key structure.

## 7.2 Elementary Sorts

Q: Describe [insertion sort] and its complexity.
A: For $i = 1, \ldots, n-1$: take element $A[i]$ and insert it into the CORRECT POSITION in the already-sorted prefix $A[0..i-1]$ by shifting larger elements right. Time: $O(n^2)$ worst-case, $O(n)$ best-case (already sorted). Space: $O(1)$. STABLE. Great for: small arrays ($n < 50$), nearly-sorted data, online sorting. Used as base case in most practical hybrid sorts.

Q: Compare INSERTION SORT, SELECTION SORT, and BUBBLE SORT.
A: All are $O(n^2)$, in-place. Insertion: $O(n)$ on nearly-sorted — ADAPTIVE; stable. Selection: always $\Theta(n^2)$ comparisons, minimum swaps ($O(n)$); not stable. Bubble: $\Theta(n^2)$, many swaps, adaptive; stable. Insertion is usually BEST among the three in practice. None are competitive for large $n$; all useful for teaching or specialized cases.

## 7.3 Mergesort

Q: Describe [mergesort] and its complexity.
A: DIVIDE-AND-CONQUER: split array in half, sort each half recursively, MERGE the two sorted halves. Merge step: two pointers advance through the two arrays, picking smaller element; $O(n)$ per merge. Total: $T(n) = 2 T(n/2) + O(n) = O(n \log n)$. Space: $O(n)$ extra for merge buffer. STABLE. Guaranteed worst-case $O(n \log n)$. Used: external sorting (large data that doesn't fit in memory), Java `Collections.sort`, Python's Timsort.

Q: Why is mergesort STABLE?
A: Because the MERGE step takes the LEFT element on ties — preserving original order. For elements with equal keys, the one appearing first in the input appears first in the output. Stability matters when secondary fields shouldn't be disturbed (e.g., sort by department, preserving alphabetical order within). Most in-place mergesorts sacrifice stability; the standard buffered version keeps it.

## 7.4 Quicksort

Q: Describe [quicksort].
A: Choose a PIVOT; PARTITION array into elements $\leq$ pivot and elements $>$ pivot; recursively sort each partition. In-place: Lomuto or Hoare partition schemes. Expected time (random pivot): $O(n \log n)$. Worst case: $O(n^2)$ (adversarial input against deterministic pivot — e.g., always picking first element on sorted input). Space: $O(\log n)$ stack. Not stable (typical).

Q: How do we AVOID quicksort's $O(n^2)$ worst case?
A: (1) RANDOMIZE pivot selection — worst-case becomes expected $O(n \log n)$ regardless of input. (2) MEDIAN-OF-THREE (pivot = median of first, middle, last) — reduces worst-case probability on common patterns. (3) INTROSPECTIVE SORT (introsort): start with quicksort; SWITCH to heapsort if recursion depth exceeds $2 \log n$. Guaranteed $O(n \log n)$ worst-case, with quicksort's constants. C++ `std::sort`.

## 7.5 Partitioning

Q: Describe LOMUTO partitioning.
A: Given array $A[\ell..r]$ and pivot $A[r]$: maintain $i = \ell - 1$. For $j = \ell, \ldots, r - 1$: if $A[j] \leq \text{pivot}$, increment $i$ and swap $A[i], A[j]$. Finally swap $A[i+1], A[r]$. Returns pivot index $i + 1$. Simple, but $O(n^2)$ on arrays with many EQUAL keys (partitions unbalanced). Hoare partitioning is more robust but slightly more complex.

Q: What is the THREE-WAY (Dutch National Flag) partition?
A: Partitions into THREE segments: $<$ pivot, $=$ pivot, $>$ pivot. Avoids $O(n^2)$ on arrays with many duplicates (e.g., sorting 0/1s, or a million copies of one value). Pointers: `low`, `mid`, `high`. Walk `mid`; swap with `low` or `high` depending on comparison. Used in Java's dual-pivot quicksort and many modern implementations.

## 7.6 The $\Omega(n \log n)$ Lower Bound

Q: State and prove the $\Omega(n \log n)$ lower bound for COMPARISON-BASED sorting.
A: Any comparison-based sort is a DECISION TREE: each internal node is a comparison, each leaf a permutation. There are $n!$ possible permutations — each must be reachable by a distinct leaf. Tree height $h$ satisfies $2^h \geq n!$, so $h \geq \log_2(n!) = \Theta(n \log n)$ by Stirling. Worst-case runtime is $\geq$ tree height. Hence no comparison sort can beat $O(n \log n)$ — settling the question for the model.

Q: Why does this LOWER bound NOT prevent linear-time sorting in general?
A: Because it only applies to COMPARISON-BASED sorts. Algorithms exploiting SPECIFIC STRUCTURE (e.g., integers in a bounded range, strings of fixed length) can beat it: COUNTING SORT, RADIX SORT, BUCKET SORT all achieve $O(n)$ or $O(n \log_b K)$ under their preconditions. The lower bound says: if all you can do is compare, you're stuck at $n \log n$.

## 7.7 Counting Sort

Q: Describe [counting sort] for integers in range $[0, k]$.
A: (1) Count occurrences of each value: $\text{count}[v] = |\{i : A[i] = v\}|$. (2) Compute cumulative sums so $\text{count}[v] = $ number of elements $\leq v$. (3) Place each element in its correct output position. Time: $O(n + k)$. Space: $O(n + k)$. STABLE (with care). EFFICIENT only if $k = O(n)$; degrades when $k \gg n$.

## 7.8 Radix Sort

Q: Describe [radix sort] and its complexity.
A: Sort integers by processing digits LSB-to-MSB (or MSB-to-LSB). Each digit pass: stable sort (usually counting sort) by that digit's value. $d$ digits, $k$ values per digit: $O(d(n + k))$. For integers up to $n^c$: $d = O(c)$ passes with $k = n$, giving $O(cn) = O(n)$ time for constant $c$. Beats comparison sort on integers! Widely used for: sorting IPs, strings, fixed-width numbers.

Q: Why does radix sort work with LSB-first ordering?
A: Because each pass uses a STABLE sort; after sorting by digit $d$, numbers with the same digit $d$ value maintain their previous order (the order from digit $d-1$'s pass, for all less-significant digits combined). After processing all $d$ digits, the array is fully sorted. STABILITY of the per-digit sort is critical; unstable sort per digit would not preserve the invariant.

## 7.9 Bucket Sort

Q: Describe [bucket sort].
A: Assume input is uniformly distributed over $[0, 1)$. Distribute elements into $n$ BUCKETS by floor(n * value); sort each bucket (e.g., insertion sort); concatenate. Expected $O(n)$ under uniform distribution. Generalizes to other distributions (adjust bucket ranges). Used in: SAMPLING-based sorting (external memory), GPU-friendly parallel sort. Adversarial (non-uniform) input → $O(n^2)$.

## 7.10 Introsort and Timsort

Q: What is [Timsort], and why does Python/Java use it?
A: Hybrid MERGE + INSERTION sort exploiting existing order in real-world data. Identifies monotonic RUNS, merges them cleverly. Key insight: real data often has PARTIALLY SORTED segments (already-sorted-ish logs, concatenated sorted lists). Timsort exploits this for $O(n)$ on nearly-sorted data. Always $O(n \log n)$ worst-case. Stable. Used in: Python's `sorted()`, Java's `Arrays.sort` (for objects), Rust's `sort()`.

## 7.11 External and Parallel Sorting

Q: What is [external sorting]?
A: Sorting data that DOES NOT FIT IN MEMORY — e.g., terabyte-scale files. Two-phase: (1) DIVIDE into chunks that fit in RAM, sort each, write to disk. (2) MERGE sorted chunks using a K-way merge (heap-based). I/O-efficient: minimizes disk reads. Hadoop/MapReduce's shuffle phase is essentially external mergesort. Standard Unix `sort` command handles gigabyte files this way.

## 7.12 A Worked Sorting Comparison

P: Sort the array $[3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]$ using quicksort with the last element as pivot. Show the first partition.
S:
**IDENTIFY**: Lomuto partition with pivot = $A[n-1] = 5$ (the last "5").

**PLAN**: Walk through array, maintaining pointer $i$ (boundary of "$\leq 5$" region). Swap when $A[j] \leq 5$.

**EXECUTE**:
Array: $A = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]$; pivot = 5 (last element); $i = -1$.

- $j = 0$: $A[0] = 3 \leq 5$. $i = 0$; swap $A[0], A[0]$ (no-op). Array unchanged.
- $j = 1$: $A[1] = 1 \leq 5$. $i = 1$; swap $A[1], A[1]$. Unchanged.
- $j = 2$: $A[2] = 4 \leq 5$. $i = 2$; swap. Unchanged.
- $j = 3$: $A[3] = 1 \leq 5$. $i = 3$; swap. Unchanged.
- $j = 4$: $A[4] = 5 \leq 5$. $i = 4$; swap $A[4], A[4]$. Unchanged.
- $j = 5$: $A[5] = 9 > 5$. Skip.
- $j = 6$: $A[6] = 2 \leq 5$. $i = 5$; swap $A[5], A[6]$: $[3, 1, 4, 1, 5, 2, 9, 6, 5, 3, 5]$.
- $j = 7$: $A[7] = 6 > 5$. Skip.
- $j = 8$: $A[8] = 5 \leq 5$. $i = 6$; swap $A[6], A[8]$: $[3, 1, 4, 1, 5, 2, 5, 6, 9, 3, 5]$.
- $j = 9$: $A[9] = 3 \leq 5$. $i = 7$; swap $A[7], A[9]$: $[3, 1, 4, 1, 5, 2, 5, 3, 9, 6, 5]$.

Final swap: swap $A[i+1], A[r] = A[8], A[10]$: $[3, 1, 4, 1, 5, 2, 5, 3, 5, 6, 9]$.

Pivot placed at index 8.

**EVALUATE**: After partition: elements to the left of index 8 are all $\leq 5$: $[3, 1, 4, 1, 5, 2, 5, 3]$ ✓. Elements to the right are $> 5$: $[6, 9]$ ✓. Pivot 5 at index 8 is now in its FINAL sorted position. Recursively sort the two partitions $[3, 1, 4, 1, 5, 2, 5, 3]$ (length 8) and $[6, 9]$ (length 2). Notice: this particular pivot choice produced an UNBALANCED partition (8 vs 2) — a reminder that pivot selection is crucial for performance. Random pivots or median-of-three would tend toward more balanced splits.
