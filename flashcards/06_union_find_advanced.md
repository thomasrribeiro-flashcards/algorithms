+++
order = 6
subject = "Computer Science"
tags = ["cs", "algorithms", "data-structures", "union-find", "disjoint-set", "segment-tree", "fenwick", "lca"]
+++

# Algorithms — Union-Find and Advanced Data Structures

## 6.1 The Disjoint-Set ADT

Q: What is the [disjoint-set] (union-find) ADT?
A: Maintains a DYNAMIC PARTITION of elements into disjoint sets, supporting: (1) MAKE-SET($x$): create a new singleton $\{x\}$. (2) FIND($x$): return the identifier of the set containing $x$. (3) UNION($x, y$): merge the sets containing $x$ and $y$. Use cases: KRUSKAL's MST, connected components, percolation, equivalence classes, online-connectivity problems.

## 6.2 Simple Union-Find Implementations

Q: Compare the LINKED LIST and TREE implementations of union-find.
A: Linked list: each set is a list with a pointer to its representative. FIND is $O(1)$; UNION is $O(n)$ (update all pointers in the smaller set). With "weighted union" (always merge smaller into larger), total cost of $m$ ops is $O(m + n \log n)$. Tree: each set is a tree; root is representative. FIND = walk to root ($O(h)$); UNION = link one root under the other. With path compression + union by rank → nearly $O(1)$ amortized (next section).

## 6.3 Union by Rank and Path Compression

Q: Describe [union by rank].
A: Each tree node has a RANK (upper bound on its subtree height). On UNION: link the ROOT with SMALLER rank under the root with larger rank. Ties: link either way, increment rank of the winner. Keeps trees SHALLOW — depth $\leq \log_2 n$ even without compression.

Q: Describe [path compression].
A: On FIND($x$): walk up to the root, then reset the parent pointer of EVERY NODE on the path to POINT DIRECTLY to the root. Subsequent finds of any node on that path are $O(1)$. Amortized cost: when combined with union by rank, TOTAL cost of $m$ operations on $n$ elements is $O(m \alpha(n))$ where $\alpha$ is the INVERSE ACKERMANN function — effectively $\leq 4$ for any practical $n < 2^{65536}$. Theoretically optimal for this problem.

## 6.4 Why Union-Find Is Near-Constant

Q: What is the [inverse Ackermann function] $\alpha(n)$?
A: Grows SO SLOWLY it's essentially constant for all practical $n$: $\alpha(n) \leq 4$ for $n < 2^{2^{2^{16}}}$, a number larger than the universe's atom count. Formally the inverse of a recursively-defined monster. Union-find with both optimizations achieves $\Theta(m \alpha(n))$ amortized — proven OPTIMAL by Fredman-Saks. In practice, you can ignore the $\alpha$ factor and treat union-find as $O(1)$ per op.

## 6.5 Segment Trees

Q: What is a [segment tree]?
A: A BINARY TREE supporting range queries on an array in $O(\log n)$. Each node stores an AGGREGATE (sum, min, max, etc.) over a contiguous range; children split the range in half. Operations: POINT UPDATE ($O(\log n)$), RANGE QUERY ($O(\log n)$). Size: $O(n)$ (roughly $2n$ to $4n$ nodes). Ubiquitous in competitive programming; also used in computational geometry (interval stabbing).

Q: How does a SEGMENT TREE handle RANGE SUM QUERIES?
A: Each internal node stores the sum of its range. POINT UPDATE: traverse from root to the leaf for index $i$ (depth $\log n$), update each node's sum on the path. RANGE QUERY for $[l, r]$: recursively split the query range across tree nodes — for each node, either its range is fully contained (use cached sum), disjoint (skip), or partially overlapping (recurse). At most $O(\log n)$ nodes visited.

## 6.6 Lazy Propagation

Q: What is [lazy propagation] in segment trees?
A: When a RANGE UPDATE spans an entire subtree, don't push it down immediately — store a LAZY flag. Propagate on demand during subsequent queries or updates that descend into children. Enables $O(\log n)$ RANGE UPDATES (e.g., "add $x$ to all elements in $[l, r]$"). Without lazy, range updates would be $O(n)$. Standard technique in modern competitive programming.

## 6.7 Fenwick (Binary Indexed) Tree

Q: What is a [Fenwick tree (BIT)], and how does it compare to segment trees?
A: A compact array-based structure supporting PREFIX-SUM queries and POINT UPDATES in $O(\log n)$. Uses LSB (lowest set bit) manipulation to navigate. Simpler to implement than segment tree, less memory ($n$ vs $\sim 4n$). But LIMITED: only supports invertible operations (sum, XOR) and can't easily handle arbitrary range aggregates. Range sum via prefix difference: $\text{sum}(l, r) = \text{prefix}(r) - \text{prefix}(l - 1)$.

Q: Sketch the CORE operations of a Fenwick tree.
A: `update(i, delta)`: while $i \leq n$: $\text{tree}[i] \mathrel{+}= \text{delta}$; $i \mathrel{+}= i \,\&\, (-i)$ (add LSB).
`query(i)` (prefix sum $1..i$): $\text{res} = 0$; while $i > 0$: $\text{res} \mathrel{+}= \text{tree}[i]$; $i \mathrel{-}= i \,\&\, (-i)$ (subtract LSB).
Both are $O(\log n)$. Key trick: the LSB encodes the "range" each tree position covers. 10 lines of code — much simpler than segment trees.

## 6.8 Trie (Prefix Tree)

Q: What is a [trie], and when is it useful?
A: A TREE where each edge is labeled with a character; paths from root to marked nodes spell out STRINGS in the stored set. Search, insert, delete a string of length $L$: $O(L)$. Excellent for: (1) PREFIX queries (all keys starting with "foo"). (2) AUTOCOMPLETE. (3) DICTIONARY spell-check. (4) ROUTING tables (IP prefix matching). (5) AHO-CORASICK multi-pattern matching. Memory-heavy for sparse keys; solved by compressed tries (PATRICIA/radix trees).

## 6.9 LCA and Euler Tour

Q: What is the [Lowest Common Ancestor (LCA)] problem?
A: Given a ROOTED TREE and two nodes $u, v$: find the deepest node that is an ancestor of both. Naïve: walk up from both until meeting; $O(n)$. With PREPROCESSING, can answer in $O(1)$ per query after $O(n)$ preprocessing, via EULER TOUR + RMQ. Used in: auto-complete (common prefix), phylogenetic trees, code dependency analysis.

Q: How does EULER TOUR + RMQ solve LCA in $O(1)$ per query?
A: Perform a DFS traversal logging nodes as you visit (including backtracks). Obtain an "Euler tour" of $2n - 1$ entries. Record FIRST-OCCURRENCE index of each node. Also record the depth of each entry. LCA($u, v$) = node with MINIMUM DEPTH in the Euler-tour range $[\text{first}(u), \text{first}(v)]$. This is the Range Minimum Query (RMQ) problem, solvable in $O(1)$ per query after $O(n)$ preprocessing via sparse tables. Elegant reduction.

## 6.10 Interval Trees and K-d Trees

Q: What is an [interval tree], and what problem does it solve?
A: Supports the INTERVAL STABBING problem: given a set of intervals, find all intervals containing a query point. Built as a balanced BST on interval midpoints; each node augmented with MAX ENDPOINT of its subtree. Query $O(\log n + k)$ where $k$ = number of hits. Used in: timeline visualization, genomic range queries, calendar apps. Generalizes to 2D (rectangles) and higher dimensions (k-d trees).

Q: What is a [k-d tree]?
A: A BST generalized to $k$-dimensional data. At depth $d$, split by coordinate $d \mod k$. Supports NEAREST NEIGHBOR and RANGE QUERIES in $O(n^{1 - 1/k} + m)$ (where $m$ = output size). Efficient for LOW dimensions ($k \leq 10$); degrades in HIGH dimensions (curse of dimensionality). Alternatives for high-$k$: ball trees, LSH (locality-sensitive hashing), approximate NN structures.

## 6.11 Persistent Data Structures

Q: What is a [persistent data structure]?
A: A data structure that PRESERVES PREVIOUS VERSIONS after updates — every modification creates a new "version" without destroying the old one. Example: persistent BST shares unchanged subtrees between versions (path-copying). Uses: functional programming (Clojure, Haskell), UNDO systems, concurrent data structures, version control (Git snapshots). Time overhead: $O(\log n)$ extra per op; space: $O(\log n)$ extra per op.

## 6.12 A Worked Union-Find Example

P: Process the sequence of operations: MAKE-SET for elements $\{1, 2, 3, 4, 5\}$; UNION(1, 2); UNION(3, 4); UNION(1, 3); FIND(2); FIND(5). Use union by rank with path compression. Show the state after each step.
S:
**IDENTIFY**: Simulate union-find with trees, showing parent pointers.

**PLAN**: Maintain parent[] and rank[] arrays. UNION by rank: smaller rank → child. FIND with path compression.

**EXECUTE**:
Initial: parent = [1, 2, 3, 4, 5] (each element is its own root), rank = [0, 0, 0, 0, 0].

UNION(1, 2): ranks equal (0). Make 2 parent of 1 (say), increment rank(2). parent = [2, 2, 3, 4, 5], rank = [0, 1, 0, 0, 0].

UNION(3, 4): similar. parent = [2, 2, 4, 4, 5], rank = [0, 1, 0, 1, 0].

UNION(1, 3): FIND(1) = 2 (rank 1), FIND(3) = 4 (rank 1). Ranks equal, tie broken say 2 under 4. parent = [2, 4, 4, 4, 5], rank = [0, 1, 0, 2, 0]. (4 is now root of {1, 2, 3, 4}.)

FIND(2): 2's parent is 4 (root). Path compression: parent[2] = 4 (already). Return 4.

FIND(5): 5's parent is 5 (itself). Return 5.

Final: {1, 2, 3, 4} with root 4; {5} with root 5.

**EVALUATE**: All FINDs after UNIONs return the correct representatives. Trees stay SHALLOW thanks to union by rank — tree heights bounded by $\log_2 n$. Path compression flattens paths lazily; next FIND(1) would compress 1's direct-to-4 pointer (already direct here). On larger workloads: $m$ ops on $n$ elements take $O(m \alpha(n))$ — essentially linear. Kruskal's MST uses exactly this: $E$ UNION-FINDs in $O(E \alpha(V))$ time, often FASTER than sorting edges (which dominates at $O(E \log E)$).
