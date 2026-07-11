+++
order = 5
subject = "computer-science"
tags = ["cs", "algorithms", "data-structures", "heap", "priority-queue", "binary-heap", "heapsort"]
+++

# Algorithms — Heaps and Priority Queues

## 5.1 The Priority Queue ADT

Q: What is the [priority queue] ADT?
A: A collection supporting INSERT(element, priority) and EXTRACT-MIN (or EXTRACT-MAX), where the extracted element has the extremal priority. Optional: DECREASE-KEY (lower an element's priority), DELETE. The "ordering by priority" makes it ideal for: Dijkstra's algorithm, Prim's MST, task scheduling, event simulation, Huffman coding, A* search. Heaps are the canonical efficient implementation.

## 5.2 Binary Heaps

C: A [binary heap] is a COMPLETE binary tree (all levels full except possibly the last, filled left-to-right) satisfying the [heap property]: in a MIN-HEAP, every parent $\leq$ its children; in a MAX-HEAP, every parent $\geq$ its children.

Q: Why is a COMPLETE binary tree ideal for implementing a heap?
A: Because it can be stored IMPLICITLY in an ARRAY with no pointers: for node at index $i$ (1-indexed), parent at $\lfloor i/2 \rfloor$, children at $2i$ and $2i + 1$. No memory wasted on pointers, excellent cache behavior. Also: completeness guarantees height $\lfloor \log_2 n \rfloor$, so all operations are $O(\log n)$.

## 5.3 Heap Operations

Q: Describe [heap INSERT (sift-up / percolate-up)] in a min-heap.
A: (1) Add new element at the NEXT AVAILABLE LEAF POSITION (maintains completeness). (2) SIFT UP: while the element is smaller than its parent, SWAP with parent. (3) Stop when element is $\geq$ parent or reaches root. Time: $O(\log n)$ in the worst case (height of tree). Total insertions of $n$ elements: $O(n \log n)$.

Q: Describe [heap EXTRACT-MIN (sift-down / percolate-down)].
A: (1) Return the ROOT (minimum). (2) Move the LAST LEAF to the root position (preserves completeness). (3) SIFT DOWN: while the root is greater than one of its children, SWAP with the SMALLER child. (4) Stop when heap property is restored or reach a leaf. Time: $O(\log n)$.

## 5.4 Building a Heap

Q: How do you BUILD-HEAP on an arbitrary array in $O(n)$?
A: Sift-down from the LAST internal node ($i = \lfloor n/2 \rfloor$) DOWN TO THE ROOT ($i = 1$). At each node, its subtree is already a valid heap by induction; sift-down restores the property rooted at $i$. Why $O(n)$? A node at height $h$ sifts down $O(h)$ levels; number of nodes at height $h$ is $O(n / 2^{h+1})$. Total: $\sum_h O(n/2^{h+1}) \cdot O(h) = O(n \sum h/2^h) = O(n)$ since $\sum h / 2^h$ converges.

Q: Why doesn't inserting $n$ elements one-by-one give $O(n)$ heap construction?
A: Because each insertion is $O(\log n)$ worst-case, giving $O(n \log n)$ total. The SIFT-DOWN approach achieves $O(n)$ because most nodes are near the BOTTOM (short sift-down distances). One-by-one insertion sifts UP, and levels near the top fill up with more nodes — but the height advantage doesn't apply the same way. This is a classic demonstration of how algorithm ORDER matters for complexity.

## 5.5 Heapsort

Q: Describe [heapsort].
A: (1) BUILD-HEAP on the input array (max-heap) in $O(n)$. (2) Repeatedly: extract max (swap root with last element, shrink heap, sift down). After $n$ extractions, the array is SORTED in place (extracted maxes accumulate at the right end). Time: $O(n \log n)$. Space: $O(1)$ (in-place). Heapsort is guaranteed $O(n \log n)$ worst-case, unlike quicksort.

Q: Compare HEAPSORT to QUICKSORT and MERGESORT.
A: Heapsort: $O(n \log n)$ worst-case, $O(1)$ extra space, NOT STABLE, poor cache behavior. Quicksort: $O(n \log n)$ expected, $O(\log n)$ stack space, not stable, excellent cache behavior; usually FASTEST in practice. Mergesort: $O(n \log n)$ worst-case, $O(n)$ extra space, STABLE. Heapsort is chosen when worst-case matters AND in-place is needed (embedded systems, safety-critical). Quicksort with introsort fallback is Python's `sorted`, Java, C++.

## 5.6 Decrease-Key and Dijkstra

Q: Why is DECREASE-KEY crucial for Dijkstra's algorithm?
A: Because Dijkstra relaxes edges, potentially LOWERING the distance estimate of an already-in-queue vertex. Efficient implementations use a priority queue supporting decrease-key. Total work: $n$ extract-mins + $m$ decrease-keys (one per edge). With binary heap: $O((n + m) \log n)$. With Fibonacci heap: $O(m + n \log n)$ — asymptotically better. Without decrease-key: re-insert with lower priority; lazily dismiss old entries on extraction.

## 5.7 Fibonacci Heaps

Q: What makes [Fibonacci heaps] theoretically superior?
A: DECREASE-KEY and INSERT are $O(1)$ AMORTIZED; EXTRACT-MIN is $O(\log n)$ amortized. Compare to binary heaps: decrease-key is $O(\log n)$. For Dijkstra with many decrease-keys per extract, Fibonacci gives $O(m + n \log n)$ vs. $O((m + n) \log n)$. Practical caveat: Fibonacci heaps have LARGE CONSTANTS and complex implementation; binary heaps or d-ary heaps are usually faster for realistic sizes.

## 5.8 Binomial and Pairing Heaps

Q: What is a [binomial heap]?
A: A FOREST of binomial trees of distinct sizes $2^0, 2^1, 2^2, \ldots$, each satisfying the heap property. Operations: UNION of two heaps in $O(\log n)$ by merging forests; INSERT is a special case. Useful when MERGE/MELD is needed frequently. Foundation for Fibonacci heaps (which lazily defer restructuring).

Q: What is a [pairing heap]?
A: A MULTIWAY tree (not binary!) with the heap property, using a simple lazy merge. $O(1)$ amortized insert; extract-min pairs up children and merges. Tight bounds are famously hard to prove: decrease-key is believed $O(\log \log n)$ amortized (not fully proven). In PRACTICE, pairing heaps often OUTPERFORM Fibonacci heaps due to simplicity and cache behavior.

## 5.9 D-ary Heaps

Q: What is a [d-ary heap], and when is it preferable to binary?
A: Generalization where each node has $d$ children (instead of 2). Height becomes $\log_d n$ — shallower. Insert/sift-up: $O(\log_d n)$. Extract-min/sift-down: $O(d \log_d n)$ (must compare with all $d$ children each step). Optimal $d$ for Dijkstra: $d = \lceil m/n \rceil$ ties extract-min and decrease-key costs. For DENSE graphs ($m \sim n^2$), $d$-ary with large $d$ approaches the performance of a sorted array.

## 5.10 Heaps Beyond Priority Queues

Q: How do you find the [k-th smallest] of $n$ elements using a heap?
A: Maintain a MAX-HEAP of size $k$ while scanning; replace the max when a smaller element arrives. $O(n \log k)$.

Q: How do you maintain the [median of a stream] using heaps?
A: TWO heaps: a max-heap of the lower half and a min-heap of the upper half, kept balanced. $O(\log n)$ per element.

Q: How do you [merge $k$ sorted lists] using a heap?
A: Min-heap over the $k$ current heads; repeatedly extract the min and insert that list's next element. $O(n \log k)$ total.

Q: What role does a heap play in [Huffman coding]?
A: Min-heap of frequencies: extract two minimums, combine, reinsert — builds the optimal prefix code.

Q: What is the priority in a heap-based [event-driven simulation]?
A: The EVENT TIME — extract-min always yields the next event to process.

## 5.11 Lazy Deletion

Q: How does LAZY DELETION help when heaps don't support decrease-key?
A: When decreasing a key, leave the old entry in the heap as a "stale" element; insert a NEW entry with the new priority. On extract-min, check if extracted element is stale (its key doesn't match current stored priority in a separate hash map) — if so, discard and extract again. Time: $O(\log n)$ amortized per real operation; max $O(n)$ heap size if many stale entries. Trade memory for code simplicity; common in practice.

## 5.12 A Worked Heap Example

P: Given array $A = [4, 10, 3, 5, 1]$, use BUILD-HEAP to create a min-heap, then extract two minimums.
S:
**IDENTIFY**: Array representation (1-indexed conceptually, but most implementations use 0-indexed). Parent at $(i-1)/2$; children at $2i+1, 2i+2$ (0-indexed). BUILD-HEAP: sift-down from index $\lfloor n/2 \rfloor - 1$ down to 0.

**PLAN**: $n = 5$. Start at index 1 (= $\lfloor 5/2 \rfloor - 1$... actually 0-indexed: start at index $(n/2) - 1 = 1$).

**EXECUTE**:
Initial: $[4, 10, 3, 5, 1]$ with indices 0-4.

Sift-down at index 1 (value 10): children at indices 3 (value 5), 4 (value 1). Smaller child: 1 (index 4). $10 > 1$, so swap. Array: $[4, 1, 3, 5, 10]$. Index 4 has no children — done.

Sift-down at index 0 (value 4): children at indices 1 (value 1), 2 (value 3). Smaller child: 1 (index 1). $4 > 1$, swap. Array: $[1, 4, 3, 5, 10]$. Now value 4 at index 1: children at 3 (value 5), 4 (value 10). Smaller child: 5; $4 < 5$, done.

Heap built: $[1, 4, 3, 5, 10]$. Valid min-heap ✓ (1 ≤ 4, 3; 4 ≤ 5, 10; 3 has no children in this size).

Extract-min: return 1. Move last element (10) to root: $[10, 4, 3, 5]$. Sift-down: children 4, 3; smaller 3 at index 2. $10 > 3$, swap: $[3, 4, 10, 5]$. Value 10 at index 2, child at index 5 (out of range, only 4 elements now; 0-indexed indices 0-3, child 5 doesn't exist). Done.

Heap after first extract: $[3, 4, 10, 5]$.

Extract-min: return 3. Last (5) to root: $[5, 4, 10]$. Sift-down: children 4, 10; smaller 4 at index 1. $5 > 4$, swap: $[4, 5, 10]$. Index 1, children out of range. Done.

**EVALUATE**: Extracted mins in order: 1, 3 — correct ascending order. After two extractions, heap is $[4, 5, 10]$. Continuing extracts would give 4, 5, 10 for full sorted output — this is heapsort! Note how BUILD-HEAP was $O(n)$ (just 2 sift-downs for 5 elements), but successive extractions are $O(\log n)$ each. For $n$ extractions: $O(n \log n)$ total — the heapsort complexity.
