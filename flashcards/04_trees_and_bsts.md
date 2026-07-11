+++
order = 4
subject = "computer-science"
tags = ["cs", "algorithms", "data-structures", "bst", "avl", "red-black", "b-tree", "tree"]
+++

# Algorithms — Binary Search Trees and Balanced Trees

## 4.1 Binary Trees

C: A [binary tree] is a tree where each node has AT MOST two children — left and right. Nodes without children are [leaves].

C: The [height] of a binary tree is the length of the longest root-to-leaf path; the [depth] of a node is the length of the root-to-node path.

Q: What are KEY binary tree traversals, and what do they produce?
A: (1) [In-order] (left, root, right): for BST, yields SORTED keys. (2) [Pre-order] (root, left, right): used to SERIALIZE trees, reconstruct structure. (3) [Post-order] (left, right, root): used for bottom-up computations (tree deletion, expression evaluation). (4) [Level-order] (BFS): queue-based, visits by depth. All are $O(n)$; choice depends on desired key ordering.

## 4.2 Binary Search Trees

C: A [binary search tree (BST)] is a binary tree satisfying the BST PROPERTY: for every node $x$, all keys in the LEFT subtree are $< x.\text{key}$, and all keys in the RIGHT subtree are $> x.\text{key}$.

Q: Why does the BST property enable efficient LOOKUP?
A: Because at each node, we can DISCARD half the tree: if the target $< x.\text{key}$, it can only be in the left subtree; if $>$, the right. Lookup time proportional to TREE HEIGHT $h$. In a BALANCED tree, $h = O(\log n)$; in a DEGENERATE tree (linear chain), $h = O(n)$. This height dependency motivates balanced trees.

## 4.3 BST Operations

Q: Describe BST INSERT and its complexity.
A: Start at root; go left if new key $<$ current, right if $>$; continue until hitting a NIL; insert as a new leaf there. Maintains BST property automatically. Time: $O(h)$ where $h$ is tree height. Key observation: inserting SORTED keys creates a LEFT-SPINE chain of height $n$ — worst-case $O(n)$ per operation. Randomizing or balancing prevents this.

Q: Describe BST DELETE and its complexity.
A: Three cases for removing node $z$: (1) No children: splice out. (2) One child: replace $z$ with its child. (3) Two children: find successor $y$ (smallest in right subtree), copy $y$'s key to $z$, recursively delete $y$ (which has at most one child). Time: $O(h)$. Symmetric predecessor variant works equally. Careful implementation avoids dangling pointers.

## 4.4 BST Search Properties

Q: What's the relationship between IN-ORDER traversal of a BST and sorting?
A: In-order traversal visits nodes in INCREASING KEY ORDER. So in-order traversal of a BST PRODUCES a sorted sequence in $O(n)$ time. Conversely: inserting $n$ elements into a BST and traversing in-order gives a tree-based sort with expected $O(n \log n)$ time (randomized BST) or worst-case $O(n^2)$ (unbalanced).

Q: Explain the RANDOMIZED BST expected height.
A: Insert $n$ keys in RANDOM order into an initially-empty BST. Expected height $O(\log n)$ (in fact $O(2.99 \log n)$). Proof intuition: the first-inserted key splits the problem; with random order, splits are balanced on average. Contrast with ADVERSARIAL (sorted) insertion: height $n$, operations degrade to $O(n)$. Randomized BSTs (treaps, etc.) give $O(\log n)$ expected WITHOUT caring about insert order.

## 4.5 Balanced Trees: AVL

Q: What is the [AVL tree] balance condition?
A: For every node, the HEIGHTS of its left and right subtrees differ by AT MOST 1 (BALANCE FACTOR $\in \{-1, 0, 1\}$). This guarantees height $O(\log n)$ — specifically, $h \leq 1.44 \log n$. Maintained by ROTATIONS after insert/delete: single rotation fixes a height-1 violation; double rotation fixes a height-2 violation where the imbalance is on the "inner" side.

Q: What are the FOUR rotation cases in AVL trees?
A: After insertion, if node $X$ is unbalanced: (1) LEFT-LEFT (inserted into left child's left subtree): right rotation on $X$. (2) RIGHT-RIGHT: left rotation on $X$. (3) LEFT-RIGHT: left rotation on $X$'s left child, then right rotation on $X$. (4) RIGHT-LEFT: right rotation on $X$'s right child, then left rotation on $X$. Each rotation is $O(1)$; insertion/deletion is $O(\log n)$ total.

## 4.6 Red-Black Trees

Q: What are the RED-BLACK TREE balance rules?
A: (1) Every node is RED or BLACK. (2) Root is BLACK. (3) Every LEAF (NIL) is BLACK. (4) A RED node has only BLACK children (no two reds in a row). (5) Every root-to-NIL path has the SAME NUMBER OF BLACK NODES. Together: longest path ≤ 2× shortest path → height $O(\log n)$. Looser than AVL but FEWER ROTATIONS per insert/delete, making red-black faster in practice for dynamic workloads.

Q: Why are RED-BLACK trees the DE FACTO standard in libraries?
A: Because they offer: (1) Guaranteed $O(\log n)$ per operation. (2) Fewer rotations than AVL (max 2-3 per insert/delete vs AVL's potential $O(\log n)$ on deletion). (3) Simple code via SENTINEL LEAVES. Used in: Linux kernel (CFS scheduler, process trees), C++ `std::map/set`, Java `TreeMap/TreeSet`. AVL beats red-black for read-heavy workloads (better balance, faster lookup); red-black wins on mixed workloads.

## 4.7 Rotations

Q: Describe a [left rotation] at node $X$.
A: Let $Y$ be $X$'s right child. Rearrange: $Y$ becomes the new parent; $X$ becomes $Y$'s left child; $Y$'s original left child becomes $X$'s new right child. Preserves in-order (BST property) — a local restructuring that changes heights. Reverse operation: RIGHT ROTATION at $Y$. Both are $O(1)$: update at most three parent pointers plus three child pointers. Fundamental building block for all balanced-tree operations.

## 4.8 B-Trees

Q: What are [B-trees], and why are they used for databases and file systems?
A: Generalization of BST: each node has between $t - 1$ and $2t - 1$ keys (for some minimum degree $t$), and between $t$ and $2t$ children. Typical $t$ is large (say 100). Height is $O(\log_t n)$ — VERY shallow for large $t$. Each node fits in one disk BLOCK (e.g., 4 KB). Minimizes disk I/O: $\log_t n$ block reads per operation ≈ 3-4 for a billion keys with $t = 100$. Used in databases (MySQL InnoDB, PostgreSQL), file systems (NTFS, ext4), key-value stores.

Q: What's the DIFFERENCE between B-tree and B+-tree?
A: B-tree: keys and data at every node. B+-tree: keys at internal nodes are only for ROUTING; DATA is ONLY at LEAVES, and leaves are linked in a list. Advantages of B+: (1) range queries scan the linked leaves — very efficient. (2) Internal nodes pack more keys per block (no data), so tree is shallower. (3) Uniform access pattern. Most databases use B+-trees.

## 4.9 Treaps

Q: What is a [treap]?
A: A BST where each node ALSO has a RANDOM PRIORITY. The tree is simultaneously a BST by KEY and a HEAP by PRIORITY. Random priorities → random tree shape → expected $O(\log n)$ height. Insertion: insert at correct BST position (becomes a leaf), then rotate upward until heap property is restored. Simple to implement; used in competitive programming for its simplicity and good performance.

## 4.10 Skip Lists

Q: What is a [skip list], and why is it an alternative to balanced trees?
A: A probabilistic data structure: multiple levels of LINKED LISTS with increasing sparsity. Top level has $O(\log n)$ nodes; bottom level has all $n$. Lookup: descend from top, moving forward until overshoot, then drop a level. Expected $O(\log n)$ per operation. SIMPLER than red-black trees, lock-free concurrent implementations are easier. Used in: Redis, LevelDB memtable, concurrent data structures.

## 4.11 Order Statistics

Q: How do you find the $k$-th smallest element in a BST in $O(\log n)$?
A: Augment each node with the SIZE of its subtree ($= 1 + \text{size(left)} + \text{size(right)}$). To find the $k$-th smallest: start at root; if $\text{size(left)} = k - 1$, root is the answer; if $k \leq \text{size(left)}$, recurse left; else recurse right with $k' = k - \text{size(left)} - 1$. Balanced tree → $O(\log n)$ time. Augmented BSTs generalize: interval trees, order-statistic trees, segment trees.

## 4.12 A Worked BST Operation

P: Insert keys 50, 30, 70, 20, 40, 60, 80 into an empty BST, then compute the in-order traversal and perform a lookup of 60.
S:
**IDENTIFY**: Sequential insertions into BST; use recursive or iterative insertion.

**PLAN**: Trace each insertion; then in-order = (left, root, right).

**EXECUTE**:
Insert 50: tree = 50.
Insert 30: 30 < 50, go left; insert as left child. Tree: 50 → (30, _).
Insert 70: 70 > 50, right. Tree: 50 → (30, 70).
Insert 20: 20 < 50 (left), 20 < 30 (left); insert. Tree: 50's left child 30 has left child 20.
Insert 40: 40 < 50 (left), 40 > 30 (right). Tree: 30 has left 20, right 40.
Insert 60: 60 > 50 (right), 60 < 70 (left). Tree: 70 has left 60.
Insert 80: 80 > 50 (right), 80 > 70 (right). Tree: 70 has right 80.

Final structure:
```
        50
       /  \
      30   70
     / \   / \
    20 40 60 80
```

In-order traversal: 20, 30, 40, 50, 60, 70, 80 ✓ (sorted).

Lookup 60: 60 > 50 → right to 70. 60 < 70 → left to 60. Found.

**EVALUATE**: Tree is perfectly balanced (height 2) because the inserted sequence happens to mimic balanced order. Lookup took 3 comparisons ≈ $\log_2 7 = 2.8$. If we had instead inserted 20, 30, 40, 50, 60, 70, 80 in sorted order, the tree would be a RIGHT-SPINE chain of height 6 — lookup of 80 takes 7 comparisons ($O(n)$). THIS is why balanced trees (AVL, red-black) matter: they guarantee balance regardless of insertion order, via rotations.
