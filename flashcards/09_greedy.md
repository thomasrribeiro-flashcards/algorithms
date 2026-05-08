+++
order = 9
subject = "Computer Science"
tags = ["cs", "algorithms", "greedy", "matroid", "huffman", "interval-scheduling", "exchange-argument"]
+++

# Algorithms — Greedy Algorithms

## 9.1 The Greedy Paradigm

Q: What is a [greedy algorithm]?
A: An algorithm that makes the LOCALLY OPTIMAL choice at each step, never revisiting decisions. Simplest possible meta-strategy; FAST (usually linear or $n \log n$). Works when the problem has OPTIMAL SUBSTRUCTURE (optimal solution contains optimal sub-solutions) AND the [greedy-choice property]: a globally optimal solution can be built by locally optimal choices. Fails for many problems (TSP, knapsack); succeeds surprisingly often.

Q: Why is PROVING a greedy algorithm correct often HARD?
A: Because "locally optimal" doesn't obviously yield globally optimal — intuition is cheap, proof is not. Standard techniques: (1) [Exchange argument]: show any non-greedy solution can be improved (or matched) by a greedy one. (2) [Greedy stays ahead]: inductively prove greedy's partial solution is at least as good as any other at each step. (3) [Matroid theory]: characterize problems where greedy provably works. Without a proof, "greedy looks right" often means "greedy is wrong."

## 9.1b Pattern Recognition: When to Try Greedy

Q: A problem asks for an optimal selection or ordering and you can describe a SIMPLE local rule (smallest, largest, earliest finish, best ratio). What's your first move?
A: Try greedy with that rule, then prove correctness via exchange argument or "stays ahead." If you can't prove it, suspect a counterexample exists — fall back to DP.

Q: A problem fits the greedy template but multiple sort orders are plausible (sort by start? finish? duration?). How do you pick?
A: Try the smallest non-trivial counterexamples for each order. The order that survives every counterexample is the candidate; then prove it formally. (For interval scheduling: finish time wins because "finish early frees the future.")

## 9.2 Interval Scheduling

Q: Describe the [interval scheduling maximization] problem and the greedy solution.
A: Given $n$ intervals $(s_i, f_i)$ (start, finish): find the maximum-size set of PAIRWISE NON-OVERLAPPING intervals. Greedy: sort by FINISH TIME; repeatedly pick the next interval whose start $\geq$ the last selected interval's finish. $O(n \log n)$ (sorting dominates). OPTIMAL.

Q: Prove the earliest-finish-time greedy is OPTIMAL for interval scheduling via exchange argument.
A: Let $G = \{g_1, g_2, \ldots\}$ be greedy's choices, $O = \{o_1, o_2, \ldots\}$ any optimal. Assume sorted by finish time. Claim: greedy's $i$-th interval finishes NO LATER than $O$'s $i$-th. Inductive argument: $g_1$ finishes first (greedy's choice). For $i > 1$: greedy picks the EARLIEST-finishing interval compatible with $g_{i-1}$; since $o_{i-1}$ finishes $\geq g_{i-1}$ (IH), $o_i$ is compatible with $g_{i-1}$, so greedy could pick $o_i$ — hence $g_i$ finishes $\leq o_i$. Conclusion: $|G| \geq |O|$.

## 9.3 Why Finish Time, Not Start Time?

Q: Why does sorting by START TIME fail for interval scheduling?
A: Counterexample: a single LONG interval $[0, 100]$ vs. many short ones $[1, 2], [3, 4], \ldots, [99, 100]$. Start-time greedy picks the long one first, blocking all others — 1 total. Finish-time greedy picks the short ones (earliest finish = $[1, 2]$), chaining many — much larger. Lesson: "finish early" frees up future opportunities; start time alone doesn't. Choosing the right sorting criterion is the art of greedy design.

## 9.4 Fractional Knapsack

Q: Describe the [fractional knapsack] problem and its greedy solution.
A: $n$ items with values $v_i$ and weights $w_i$; knapsack capacity $W$. You may take FRACTIONS of items. Maximize total value. Greedy: sort by VALUE-TO-WEIGHT RATIO $v_i / w_i$ descending; take whole items until none fits whole, then take a fraction of the next. $O(n \log n)$, OPTIMAL. Contrast with 0/1 knapsack (indivisible items) — greedy FAILS there; requires DP (chapter 10).

Q: Why does greedy FAIL for 0/1 knapsack?
A: Because you can't "fill up" with fractions. Example: $W = 50$; items: $(v, w) = (60, 10), (100, 20), (120, 30)$. Ratios: 6, 5, 4. Greedy takes first ($v = 60$), then second ($w = 30$ total), can't fit third. Total 160. OPTIMAL: skip the top-ratio item, take the last two ($w = 50$, $v = 220$). Greedy's 160 < optimal 220. Need DP for combinatorial (integer) knapsack.

## 9.5 Huffman Coding

Q: Describe [Huffman's algorithm] as a greedy algorithm.
A: Construct optimal prefix code for symbols with frequencies. Greedy: repeatedly MERGE the two LOWEST-frequency symbols/trees into a combined node with summed frequency. Final tree's paths = codewords. $O(n \log n)$ with a priority queue. OPTIMAL (minimum expected code length). Greedy choice: the two least-frequent symbols have the DEEPEST codewords in the optimal tree — pairing them never hurts.

Q: Prove HUFFMAN's algorithm optimal via exchange argument.
A: Let $x, y$ be the two lowest-frequency symbols. Claim: some optimal prefix code has $x, y$ at the DEEPEST level, as SIBLINGS. Proof: take any optimal tree; swap $x$ with a deepest leaf $a$, and $y$ with a deepest leaf $b$ — doesn't increase cost (since $x, y$ have $\leq$ frequency, moving them deeper costs $\leq$ moving deepest elements up). Having established greedy's choice, induct on the merged tree. Greedy matches optimal at each step.

## 9.6 Activity Selection Variants

Q: How does [weighted interval scheduling] differ from unweighted, and why is greedy insufficient?
A: Weighted: each interval has a value $v_i$; maximize TOTAL VALUE (not count) of non-overlapping selections. Greedy by finish time fails when a long, high-value interval conflicts with many short low-value ones. Requires DP: sort by finish time, compute $M[i] = \max(M[i-1], v_i + M[p(i)])$ where $p(i)$ is the last non-conflicting interval before $i$. $O(n \log n)$.

## 9.7 Minimum Spanning Tree Preview

Q: How are KRUSKAL and PRIM algorithms GREEDY?
A: Kruskal: sort edges by weight; greedily add each if it doesn't form a cycle (use union-find). Prim: grow one tree; greedily add the cheapest edge from the tree to a non-tree vertex (use priority queue). Both are optimal for MST due to the [cut property]: the minimum-weight edge crossing any cut is in every MST. Greedy + cut property = optimality. Covered in Chapter 12.

## 9.8 Matroids and Greedy

Q: What is a [matroid], and why does greedy provably work on matroids?
A: A matroid is a set system $(E, \mathcal{I})$ satisfying: (1) $\emptyset \in \mathcal{I}$; (2) HEREDITARY: subsets of independent sets are independent; (3) EXCHANGE: if $A, B \in \mathcal{I}$ with $|A| < |B|$, then $\exists x \in B \setminus A$ with $A \cup \{x\} \in \mathcal{I}$. Greedy algorithm (add heaviest/cheapest element keeping independence) finds the optimal weighted basis. Theorem: greedy works on $(E, \mathcal{I})$ iff $(E, \mathcal{I})$ is a matroid. Elegant characterization.

Q: Give an EXAMPLE of a matroid.
A: [Graphic matroid]: edges of a graph with $\mathcal{I}$ = FORESTS (cycle-free subsets). Exchange property holds. Kruskal's MST = greedy on the graphic matroid with edge weights. Other matroids: LINEAR matroid (linearly independent vectors), UNIFORM matroid (any set of size $\leq k$), PARTITION matroid. Matroid theory unifies MANY greedy success stories.

## 9.9 When Greedy Fails

Q: Name classical problems where GREEDY FAILS and requires a stronger approach.
A: (1) [0/1 knapsack] — needs DP. (2) [Traveling salesperson] — NP-hard; greedy gives $O(\log n)$-approximation at best. (3) [General graph coloring] — greedy uses $O(\Delta + 1)$ colors; optimal can be far fewer. (4) [Shortest path in graphs with NEGATIVE edges] — Dijkstra (greedy) fails; need Bellman-Ford. (5) [Change-making with arbitrary denominations] — greedy fails (try 25, 10, 1 with target 30: greedy 25+1+1+1+1+1 = 6 coins; optimal 10+10+10 = 3).

## 9.10 Exchange Argument Template

Q: Describe the GENERAL TEMPLATE for an exchange-argument correctness proof.
A: (1) Let $G$ = greedy's solution, $O$ = optimal. Assume $G \neq O$. (2) Find a POSITION where $G$ and $O$ differ — typically the earliest disagreement. (3) SWAP (exchange) an element of $O$ to match $G$ at that position; show the result is still valid and no worse than $O$. (4) Iterate: eventually transform $O$ into $G$ with value $\geq O$. Hence $G$ is optimal. Standard technique for proving greedy correctness.

## 9.11 Approximation via Greedy

Q: How does greedy serve as an APPROXIMATION algorithm for NP-hard problems?
A: Even when NOT optimal, greedy often has provable approximation ratios: (1) [Set cover]: greedy gives $H_n \approx \ln n$-approximation (tight). (2) [Vertex cover]: 2-approximation via "take both endpoints of an uncovered edge." (3) [Max cut]: random/greedy achieves 1/2 of optimum. Greedy approximations are fast, simple, often near-optimal in practice. Combined with LP rounding (chapter), they form the backbone of modern approximation algorithms.

## 9.12 A Worked Greedy Problem

P: You have $n$ jobs with processing times $p_1, \ldots, p_n$ on a single machine. Minimize the TOTAL COMPLETION TIME (sum of each job's completion time). What's the greedy strategy, and why does it work?
S:
**IDENTIFY**: If jobs are scheduled in order $\pi$, job $\pi(i)$ completes at time $\sum_{j \leq i} p_{\pi(j)}$. Total completion time $= \sum_i (\text{number of jobs completed by time } t_i)$ — weighted by processing times.

**PLAN**: Order matters. Try: SHORTEST PROCESSING TIME FIRST (SPT).

**EXECUTE**: Claim: scheduling in order of NON-DECREASING $p_i$ minimizes total completion time.

Exchange argument: suppose optimal has $p_i > p_j$ with job $i$ BEFORE job $j$ consecutively. Swap them. Job $i$'s completion time goes UP by $p_j$; job $j$'s completion time goes DOWN by $p_i$. Net change: $p_j - p_i < 0$. Total completion time strictly DECREASED — contradicting optimality.

Hence any optimal schedule has jobs in non-decreasing $p_i$ order. SPT is optimal.

Algorithm: sort jobs by $p_i$; schedule in that order. $O(n \log n)$.

**EVALUATE**: Intuition: a LONG job delays EVERY job after it; schedule long jobs LATE so they delay fewer. Precisely: position $k$ (from the end) contributes its processing time to $k$ completion times. SPT puts smallest $p$s where they're multiplied by LARGE $k$ (many jobs after) — minimizing total. Generalizes to WEIGHTED total completion time (Smith's rule: sort by $p_i / w_i$). Fails when there are PRECEDENCE constraints — becomes NP-hard in general.
