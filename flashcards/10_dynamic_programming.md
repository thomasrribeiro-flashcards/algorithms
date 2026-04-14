+++
order = 10
subject = "Computer Science"
tags = ["cs", "algorithms", "dynamic-programming", "memoization", "optimal-substructure", "knapsack", "lcs"]
+++

# Algorithms — Dynamic Programming

## 10.1 The Dynamic Programming Paradigm

Q: What is [dynamic programming (DP)]?
A: A technique for solving problems with OVERLAPPING SUBPROBLEMS and OPTIMAL SUBSTRUCTURE by storing subproblem results in a table. Trades MEMORY for TIME: avoid recomputing the same subproblem. Two styles: (1) [TOP-DOWN / memoization]: recursive, cache results. (2) [BOTTOM-UP / tabulation]: iterative, fill table in dependency order. DP converts exponential brute-force into polynomial time.

Q: What TWO properties must a problem satisfy for DP to apply?
A: (1) [Optimal substructure]: an optimal solution for the problem can be constructed from optimal solutions of its subproblems (recursive structure). (2) [Overlapping subproblems]: the recursive decomposition REVISITS the same subproblems many times. Without overlapping: divide-and-conquer suffices (no caching gain). Without optimal substructure: subproblem decomposition is wrong; DP won't yield optimal.

## 10.2 Fibonacci: The Canonical Example

Q: Compare computing $F(n)$ via naïve recursion vs DP.
A: Naïve: $F(n) = F(n-1) + F(n-2)$ with $F(0) = 0, F(1) = 1$. Tree of calls has $\sim \phi^n$ leaves — EXPONENTIAL, with massive overlap. DP (memoized or bottom-up): store $F(0), F(1), \ldots, F(n)$ once; $O(n)$ time, $O(n)$ space. Further: track only LAST TWO values → $O(1)$ space, still $O(n)$ time. Converts $O(1.618^n)$ to $O(n)$ — a dramatic speedup via caching.

## 10.3 Memoization vs Tabulation

Q: Compare [MEMOIZATION] (top-down DP) and [TABULATION] (bottom-up DP).
A: Memoization: write the recursive function naturally; add a cache (dict/array) for already-computed results. Pros: only computes NEEDED subproblems; natural for sparse state spaces. Cons: recursion overhead, possible stack overflow. Tabulation: iterate in dependency order, filling a table. Pros: no recursion; better cache behavior; can often reduce space via rolling arrays. Cons: must identify ordering; computes ALL subproblems. Choose based on sparsity and language (recursion costs).

## 10.4 The DP Recipe

Q: Describe the FIVE-STEP recipe for designing a DP algorithm.
A: (1) [Define the state]: what parameters uniquely describe a subproblem? (2) [State space size]: how many subproblems? (3) [Base cases]: trivial subproblems with direct answers. (4) [Recurrence]: how does a subproblem's answer depend on smaller ones? (5) [Order of computation]: in what order do we fill the table? Total time = (state space size) × (work per state). Space = state space size (possibly reducible).

## 10.5 Longest Common Subsequence

Q: State the [Longest Common Subsequence (LCS)] problem.
A: Given two sequences $X = x_1 \ldots x_m$ and $Y = y_1 \ldots y_n$: find the longest sequence appearing as a SUBSEQUENCE in both (not necessarily contiguous). Used in: diff tools, bioinformatics (DNA alignment), file version control. Brute force: $O(2^m \cdot n)$; DP: $O(mn)$ time, $O(mn)$ or $O(\min(m, n))$ space.

Q: Write the LCS recurrence.
A: Let $L[i][j]$ = LCS length of $x_1 \ldots x_i$ and $y_1 \ldots y_j$. Base: $L[0][j] = L[i][0] = 0$. Recurrence: if $x_i = y_j$: $L[i][j] = L[i-1][j-1] + 1$ (match; extend diagonal). Otherwise: $L[i][j] = \max(L[i-1][j], L[i][j-1])$ (drop from one side). Final answer: $L[m][n]$. Reconstruct the subsequence by backtracking through the DP table.

## 10.6 0/1 Knapsack

Q: Describe the [0/1 knapsack] DP.
A: $n$ items with weights $w_i$, values $v_i$; knapsack capacity $W$; each item taken whole or not at all. State: $K[i][w]$ = maximum value using first $i$ items with weight limit $w$. Recurrence: $K[i][w] = \max(K[i-1][w], v_i + K[i-1][w - w_i])$ (skip or take item $i$). Base: $K[0][w] = 0$. Answer: $K[n][W]$. Time: $O(nW)$. Space: $O(nW)$ or $O(W)$ with rolling array.

Q: Why is 0/1 knapsack $O(nW)$ considered "pseudo-polynomial"?
A: Because it's polynomial in the NUMERIC value of $W$, but EXPONENTIAL in the BIT LENGTH of $W$. If $W$ has $k$ bits, runtime is $O(n \cdot 2^k)$. For $W = 10^{18}$ (60 bits), $O(nW)$ is infeasible even though $W$ is "one number." Knapsack is NP-hard; the $O(nW)$ algorithm is fast only when $W$ is small. True polynomial time in $n + \log W$ is impossible unless P = NP.

## 10.7 Edit Distance

Q: Describe [edit distance] (Levenshtein distance) via DP.
A: Minimum number of SINGLE-CHARACTER OPERATIONS (insert, delete, substitute) to transform string $X$ into $Y$. State: $E[i][j]$ = edit distance from $x_1 \ldots x_i$ to $y_1 \ldots y_j$. Recurrence: if $x_i = y_j$, $E[i][j] = E[i-1][j-1]$ (match, no cost); else $E[i][j] = 1 + \min(E[i-1][j], E[i][j-1], E[i-1][j-1])$ (delete, insert, substitute). Base: $E[i][0] = i$, $E[0][j] = j$. Time: $O(mn)$.

Q: What are practical APPLICATIONS of edit distance?
A: (1) SPELL CHECKERS ("did you mean...?"). (2) DIFF UTILITIES and version control (line-level edit distance). (3) DNA SEQUENCE ALIGNMENT (Needleman-Wunsch, Smith-Waterman are weighted generalizations). (4) OCR and speech recognition (comparing candidate transcriptions). (5) PLAGIARISM DETECTION. The DP framework extends naturally to weighted operations (costs differ per character) and local alignments.

## 10.8 Matrix Chain Multiplication

Q: Describe the [matrix chain multiplication] problem and DP solution.
A: Given matrices $A_1, A_2, \ldots, A_n$ with compatible dimensions: find the PARENTHESIZATION minimizing total scalar multiplications. Different orderings have vastly different costs (e.g., $30 \times 1 \cdot 1 \times 40 \cdot 40 \times 10$ vs different groupings). State: $M[i][j]$ = min cost for $A_i \cdots A_j$. Recurrence: $M[i][j] = \min_{i \leq k < j}(M[i][k] + M[k+1][j] + p_{i-1} \cdot p_k \cdot p_j)$ (split at $k$). Time: $O(n^3)$.

## 10.9 Shortest Paths via DP

Q: How does the [Floyd-Warshall] algorithm use DP?
A: Computes ALL-PAIRS SHORTEST PATHS in $O(V^3)$. State: $D^{(k)}[i][j]$ = shortest path from $i$ to $j$ using only intermediate vertices in $\{1, \ldots, k\}$. Recurrence: $D^{(k)}[i][j] = \min(D^{(k-1)}[i][j], D^{(k-1)}[i][k] + D^{(k-1)}[k][j])$ (either don't use $k$ or use $k$ as intermediate). $O(V^3)$ time, $O(V^2)$ space (overwrite in place). Works with negative edges (but no negative cycles).

Q: How does Bellman-Ford single-source shortest path use DP?
A: State: $D^{(k)}[v]$ = shortest path from source $s$ to $v$ using at most $k$ edges. Recurrence: $D^{(k)}[v] = \min(D^{(k-1)}[v], \min_{u \to v} D^{(k-1)}[u] + w(u, v))$. After $V - 1$ iterations, converges to shortest paths (assuming no negative cycles). Time: $O(VE)$. Detects negative cycles: if any distance decreases at iteration $V$, negative cycle exists. Crucial for distance-vector routing protocols.

## 10.10 Interval and Subset DP

Q: What's the DIFFERENCE between INTERVAL DP and SUBSET DP?
A: Interval DP: state is a range $[i, j]$; recurrence splits into sub-intervals. Examples: matrix chain, optimal BST construction, palindrome partitioning. Usually $O(n^3)$ time (three nested loops over $i, j, k$). Subset DP: state is a SUBSET $S$ of items; recurrence extends $S$. Example: TSP with bitmasking, $O(2^n n^2)$. Subset DP handles EXPONENTIAL state spaces — only feasible for $n \leq $ ~20.

## 10.11 Space Optimization

Q: How can DP SPACE be reduced via rolling arrays?
A: If $dp[i][\cdot]$ only depends on $dp[i-1][\cdot]$, keep only TWO ROWS (current and previous), swapping each iteration. Reduces $O(n m)$ to $O(m)$. Applies to LCS, edit distance, 0/1 knapsack. Even tighter: if $dp[i][j]$ only depends on $dp[i-1][j]$ and $dp[i][j-1]$, ONE ROW with careful order works. Recovering the SOLUTION (not just optimal value) may require more memory or "Hirschberg's algorithm" (divide-and-conquer DP).

## 10.12 A Worked DP Problem

P: Given coin denominations $\{1, 5, 10, 25\}$ and a target amount $T$: find the MINIMUM NUMBER OF COINS summing to $T$.
S:
**IDENTIFY**: Classical DP — UNBOUNDED knapsack (infinite supply of each coin). Greedy works for US coins but not arbitrary denominations — safe to use DP.

**PLAN**: State: $dp[t]$ = min coins to make amount $t$. Base: $dp[0] = 0$. Recurrence: $dp[t] = 1 + \min_{c : c \leq t} dp[t - c]$ over all denominations.

**EXECUTE**:
```
def min_coins(T, coins):
    dp = [infinity] * (T + 1)
    dp[0] = 0
    for t in range(1, T + 1):
        for c in coins:
            if c <= t:
                dp[t] = min(dp[t], 1 + dp[t - c])
    return dp[T]
```

Example: $T = 30$, coins = $\{1, 5, 10, 25\}$.
$dp[0] = 0$.
$dp[1] = 1 + dp[0] = 1$ (one 1¢).
$dp[5] = 1 + dp[0] = 1$ (one 5¢).
$dp[10] = 1 + dp[0] = 1$ (one 10¢).
$dp[25] = 1 + dp[0] = 1$.
$dp[30] = \min(1 + dp[29], 1 + dp[25], 1 + dp[20], 1 + dp[5]) = \min(\ldots, 1 + 1, 1 + 2, 1 + 1) = 2$ (25 + 5 OR 10 + 10 + 10 is 3; 25+5 is 2).

So $dp[30] = 2$.

**EVALUATE**: Time $O(T \cdot k)$ where $k = 4$ coins. Space $O(T)$. For US coins, greedy works by accident: always take largest $\leq T$. But for denominations like $\{1, 3, 4\}, T = 6$: greedy gives $4 + 1 + 1 = 3$ coins; DP finds $3 + 3 = 2$ coins. DP always correct; greedy only for "canonical" systems (checkable via Pearson's test). Variant: count NUMBER OF WAYS to make change — same DP structure, different recurrence (sum instead of min).
