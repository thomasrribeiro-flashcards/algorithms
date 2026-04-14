+++
order = 2
subject = "Computer Science"
tags = ["cs", "algorithms", "data-structures", "array", "linked-list", "stack", "queue", "dynamic-array"]
+++

# Algorithms — Elementary Data Structures

## 2.1 Arrays

C: An [array] stores $n$ elements in CONTIGUOUS memory, indexed by integers $0, 1, \ldots, n-1$. Element access takes [$O(1)$] time given the index.

Q: Why is array access $O(1)$?
A: Because the memory ADDRESS of element $i$ is computed by $\text{base} + i \cdot \text{stride}$ — a single arithmetic operation, independent of $n$. Contrast with linked lists where reaching element $i$ requires $O(i)$ pointer dereferences. This $O(1)$ access is foundational: sorting, hashing, binary search all rely on it.

Q: What are the TRADE-OFFS of arrays?
A: Pros: (1) $O(1)$ access by index; (2) cache-friendly (contiguous memory); (3) simple. Cons: (1) FIXED SIZE (classical arrays); (2) insertion/deletion in the MIDDLE is $O(n)$ (must shift); (3) wasted space if over-allocated. Dynamic arrays (below) fix the size issue; linked lists fix middle-insertion at the cost of access time.

## 2.2 Dynamic Arrays

Q: How does a [dynamic array] handle arbitrary growth?
A: Start with a fixed-size underlying array. When full, ALLOCATE A LARGER ARRAY (typically 2× capacity) and copy elements over. Amortized cost per insertion: $O(1)$. Used in Python `list`, C++ `std::vector`, Java `ArrayList`. Key insight: doubling (not incrementing) is what makes amortized cost constant — geometric growth makes copying "pay for itself."

Q: Why is amortized insertion $O(1)$ for dynamic arrays with DOUBLING?
A: Consider inserting $n$ elements starting from capacity 1. Resizes occur at sizes $1, 2, 4, 8, \ldots, n/2, n$ — copying $1 + 2 + 4 + \cdots + n < 2n$ elements TOTAL. Plus $n$ insertions themselves. Total work: $O(n)$; per insertion: $O(1)$ amortized. If growth factor is $1 + \epsilon$ with $\epsilon > 0$, amortized remains $O(1)$; if constant addition (no multiplicative growth), it becomes $O(n)$ per insert — disastrous.

## 2.3 Linked Lists

C: A [linked list] stores elements in nodes, each containing a VALUE and a POINTER to the next node. Head pointer identifies the start.

Q: Compare SINGLY-LINKED vs DOUBLY-LINKED lists.
A: Singly-linked: each node points to next only. Saves memory; only forward traversal; deletion given a node requires finding predecessor ($O(n)$). Doubly-linked: each node points to both prev and next. More memory; backward traversal; constant-time deletion given the node (splice out). Doubly-linked is standard for general-purpose lists, LRU caches, etc.

Q: What does a linked list offer that arrays DON'T?
A: $O(1)$ insertion/deletion at the HEAD or at a KNOWN NODE (given a pointer). Arrays require $O(n)$ shifting. Downside: $O(n)$ random access (no index-based lookup). Ideal for queues, stacks, and situations requiring frequent middle-insertion when you already have a pointer to the insertion point (e.g., removing a node from a hash collision chain).

## 2.4 Stacks

C: A [stack] is a LIFO (Last-In-First-Out) container supporting PUSH (add to top) and POP (remove from top) in $O(1)$.

Q: What are canonical USES of stacks?
A: (1) FUNCTION CALL management (call stack). (2) EXPRESSION evaluation: operator precedence parsing, postfix evaluation. (3) DFS (explicit or recursive). (4) UNDO/REDO in editors. (5) MATCHED brackets/parens checking. (6) BROWSER back button. Ubiquitous because "last thing we saved is next thing we need" is a common pattern.

Q: How do you implement a STACK with $O(1)$ push/pop?
A: Either: (1) ARRAY-BACKED — maintain a top-of-stack index; push = write, increment; pop = read, decrement. Dynamic array handles overflow. (2) LINKED LIST — push = new head node, old head as next; pop = remove head. Both are $O(1)$. Array version has better cache behavior; linked version never resizes. Often choose array-backed for simplicity and speed.

## 2.5 Queues

C: A [queue] is a FIFO (First-In-First-Out) container supporting ENQUEUE (add to rear) and DEQUEUE (remove from front) in $O(1)$.

Q: What are canonical USES of queues?
A: (1) BFS (breadth-first search in graphs). (2) TASK scheduling (print queues, OS processes). (3) BUFFERING between producer/consumer. (4) EVENT queues in simulations. (5) LEVEL-ORDER tree traversal. Whenever "handle in arrival order" is needed.

Q: How do you implement a QUEUE with $O(1)$ enqueue/dequeue?
A: Array-backed CIRCULAR BUFFER with head/tail pointers, wrapping around. Or linked list with both head and tail pointers. Circular buffer is simpler if max size is known; linked version dynamically grows. Naïve: dequeue from front of array requires shifting $O(n)$ — AVOID. Circular buffer fixes this with modular indexing.

## 2.6 Deques

Q: What is a [deque (double-ended queue)], and when is it useful?
A: Supports $O(1)$ INSERT and REMOVE at both ends. More flexible than stack or queue alone. Uses: sliding window maximum (keep candidates in deque), work-stealing schedulers, Aho-Corasick. Implementation: circular buffer (array-backed) or doubly-linked list. Python `collections.deque`, C++ `std::deque`.

## 2.7 Multidimensional Arrays

Q: How is a 2D ARRAY $A[m][n]$ laid out in memory?
A: Two conventions: (1) [Row-major] (C, Python): row 0, row 1, ..., stored contiguously — address of $A[i][j]$ = base + $(i \cdot n + j) \cdot$ stride. (2) [Column-major] (Fortran, MATLAB): column 0, column 1, ..., contiguous. Iteration should MATCH layout for cache efficiency: in C, loop over $j$ inside $i$; in Fortran, the opposite. Mismatching layout can cause 10× slowdown on large matrices.

## 2.8 Abstract Data Types vs Implementations

Q: What's the DIFFERENCE between an abstract data type (ADT) and a data structure?
A: An ADT SPECIFIES operations and behavior (e.g., "stack supports push, pop, peek in LIFO order"). A data structure IMPLEMENTS the ADT with a specific representation (e.g., array-backed stack). Multiple implementations can realize the same ADT with different performance profiles. Good software design separates these: use the ADT interface; switch implementations as needs change. Common ADTs: list, set, map, priority queue, graph.

## 2.9 Memory Overhead

Q: Compare MEMORY overhead of arrays vs linked lists.
A: Array: essentially zero per-element overhead (just stride-sized cells). Dynamic array: up to 2× over-allocation on average (amortization). Linked list: every node has a POINTER (8 bytes on 64-bit systems) per link — singly-linked adds $\sim 8$ bytes per element; doubly-linked $\sim 16$. For small elements (e.g., int = 4 bytes), linked lists use $3\times$ to $5\times$ more memory. Array is usually the default; use linked only when you need $O(1)$ mid-insertion.

## 2.10 Cache Behavior

Q: Why do ARRAYS often beat linked lists in practice, even for theoretically-equivalent operations?
A: CACHE behavior. Contiguous memory means sequential accesses HIT THE SAME CACHE LINE. Linked lists scatter nodes across memory — each dereference may trigger a cache miss (100 ns vs 1 ns). For sequential iteration, arrays can be 50× faster. For "array pays $O(\log n)$ binary search vs linked pays $O(n)$ scan" — array wins on BOTH complexity and cache.

## 2.11 Iterators and Range-Based Abstractions

Q: What is an [iterator], and why is it useful?
A: An abstraction for SEQUENTIAL traversal independent of underlying representation. Implements operations like "current element," "advance," "end?". Lets generic algorithms (sort, reduce, map) work on any iterable data structure — array, list, tree, stream — through a uniform interface. Central to functional programming, STL (C++), and Python's iterator protocol. Allows lazy evaluation (generators) and pipelines without materializing intermediate collections.

## 2.12 A Worked Implementation

P: Implement a CIRCULAR BUFFER queue with capacity $k$ supporting $O(1)$ enqueue, dequeue, front, and size.
S:
**IDENTIFY**: Fixed-capacity FIFO queue backed by an array; use head/tail indices with modular arithmetic.

**PLAN**: Fields: `buffer` (size $k$), `head` (front index), `count` (number of elements). Enqueue at `(head + count) mod k`; dequeue at `head`.

**EXECUTE**:
```
class CircularBuffer:
    def __init__(self, k):
        self.buf = [None] * k
        self.head = 0
        self.count = 0
        self.k = k

    def enqueue(self, x):
        if self.count == self.k: raise OverflowError
        self.buf[(self.head + self.count) % self.k] = x
        self.count += 1

    def dequeue(self):
        if self.count == 0: raise EmptyError
        x = self.buf[self.head]
        self.buf[self.head] = None
        self.head = (self.head + 1) % self.k
        self.count -= 1
        return x

    def front(self): return self.buf[self.head]
    def size(self): return self.count
```

**EVALUATE**: All operations are $O(1)$: modular arithmetic is constant-time; no shifting or copying. Memory: $O(k)$ fixed (no dynamic growth). Buffer wraps: if $k = 5$, head = 3, count = 4, elements occupy positions 3, 4, 0, 1. Overflow when count == $k$ — signals user to expand capacity (or switch to resizable queue). Used in: ring buffers for audio/video streams, UART byte buffers, lock-free concurrent queues (more subtle but same idea). Compare to linked-list queue: no memory allocation per enqueue/dequeue, much better cache behavior.
