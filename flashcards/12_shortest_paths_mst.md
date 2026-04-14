+++
order = 12
subject = "Computer Science"
tags = ["cs", "algorithms", "graph", "shortest-paths", "dijkstra", "bellman-ford", "mst", "kruskal", "prim"]
+++

# Algorithms — Shortest Paths and Minimum Spanning Trees

## 12.1 The Shortest Path Problem

Q: What are the FOUR standard variants of shortest-path problems?
A: (1) [Single-source single-target]: one source, one destination. (2) [Single-source all-targets]: distances from $s$ to every vertex. (3) [All-pairs]: distances between all pairs of vertices. (4) [Single-target all-sources]: distances from every vertex to a target (= single-source on reversed graph). Algorithms differ based on edge weights (non-negative, general, unit) and graph structure.

## 12.2 Dijkstra's Algorithm

Q: Describe [Dijkstra's algorithm].
A: Single-source shortest path for NON-NEGATIVE EDGE WEIGHTS. Maintains $dist[v]$ = best-known distance from source. Greedy: repeatedly extract the UNPROCESSED vertex $u$ with minimum $dist[u]$; RELAX each edge $(u, v)$: $dist[v] \leftarrow \min(dist[v], dist[u] + w(u, v))$. Use priority queue. Time: $O((V + E) \log V)$ with binary heap; $O(V \log V + E)$ with Fibonacci heap. $O(V^2)$ with simple array (best when dense).

Q: Why does Dijkstra's greedy approach work — intuitively?
A: Because non-negative weights mean once a vertex $u$ is extracted with minimum $dist[u]$, no future RELAXATION can improve it: any other path to $u$ must go through an unvisited vertex with $dist \geq dist[u]$, plus non-negative extra weight — no shorter. This LOCKS IN $dist[u]$ as final. With NEGATIVE edges, this logic fails (future cheap-but-long path can beat an early "settled" path).

Q: Why does Dijkstra FAIL with negative edges?
A: Example: $s \to a$ (weight 3), $s \to b$ (weight 2), $b \to a$ (weight $-2$). Dijkstra extracts $b$ first (dist 2), then $a$ via $s$ (dist 3) — locking $a$ at 3. But via $s \to b \to a$: dist is $2 + (-2) = 0$. Dijkstra missed the shorter path. Fix: Bellman-Ford (iteratively relax all edges, no "lock-in"). Johnson's algorithm (reweight to make non-negative, then Dijkstra).

## 12.3 Bellman-Ford Algorithm

Q: Describe the [Bellman-Ford algorithm].
A: Single-source shortest path handling NEGATIVE edges. Repeat $V - 1$ times: RELAX every edge. Correctness: after $k$ iterations, $dist[v]$ is correct for shortest paths using $\leq k$ edges. Shortest path uses $\leq V - 1$ edges (no cycles), so $V - 1$ iterations suffice. Time: $O(VE)$. [Negative cycle detection]: run one more iteration; if any $dist$ decreases, a negative cycle is reachable from source.

Q: When is Bellman-Ford USED in practice despite being slower than Dijkstra?
A: (1) Graphs with NEGATIVE WEIGHTS (e.g., currency arbitrage — negative cycle = arbitrage opportunity). (2) DISTANCE-VECTOR routing protocols (RIP): each router does iterative relaxation from neighbors, matching Bellman-Ford's structure. (3) SUBROUTINE in Johnson's all-pairs algorithm. Otherwise Dijkstra dominates Bellman-Ford due to the $O(VE)$ cost.

## 12.4 Floyd-Warshall

Q: Describe [Floyd-Warshall] for all-pairs shortest paths.
A: DP: $D^{(k)}[i][j]$ = shortest path from $i$ to $j$ using intermediates in $\{1, \ldots, k\}$. Recurrence: $D^{(k)}[i][j] = \min(D^{(k-1)}[i][j], D^{(k-1)}[i][k] + D^{(k-1)}[k][j])$. Implement as three nested loops over $k, i, j$: $O(V^3)$ time, $O(V^2)$ space (overwrite). Handles negative edges (no negative cycles). Simple to code; usually preferred over $V$ runs of Dijkstra for DENSE graphs.

Q: Compare FLOYD-WARSHALL vs JOHNSON'S algorithm for all-pairs.
A: Floyd-Warshall: $O(V^3)$, simple, handles negative edges. Johnson's: $O(V^2 \log V + VE)$ — reweight via Bellman-Ford, then $V$ runs of Dijkstra. Johnson's faster on SPARSE graphs ($E \ll V^2$); FW faster on DENSE. Both handle negative edges (no cycles). Johnson's is more complex to implement but asymptotically better for realistic graphs.

## 12.5 A* Search

Q: What is the [A* algorithm]?
A: Goal-directed shortest path: Dijkstra enhanced with a HEURISTIC $h(v)$ estimating distance from $v$ to the goal. Extract vertices by priority $dist[v] + h(v)$ (not just $dist[v]$). If $h$ is [ADMISSIBLE] (never overestimates) and [CONSISTENT] (satisfies triangle inequality): A* finds the optimal path, often much faster than Dijkstra by pruning the search toward the goal. Used in AI, pathfinding (games, robotics, route planning).

## 12.6 Minimum Spanning Trees

C: A [spanning tree] of an undirected graph is an acyclic connected subgraph including ALL vertices. A [minimum spanning tree (MST)] minimizes total edge weight.

Q: What's the REAL-WORLD significance of MSTs?
A: (1) NETWORK design: minimum cost to connect all cities/offices. (2) CLUSTERING: remove longest edges in MST → clusters. (3) APPROXIMATION algorithms: MST-based 2-approx for metric TSP. (4) IMAGE SEGMENTATION, circuit layout, phylogenetic tree inference. Universal: any time you need a "cheap connectivity structure," MST is the answer.

## 12.7 Kruskal's Algorithm

Q: Describe [Kruskal's MST algorithm].
A: (1) Sort all edges by weight (ascending). (2) Process edges in order: add an edge if it doesn't create a cycle (use UNION-FIND to check). (3) Stop when $V - 1$ edges are added. Time: $O(E \log E)$ for sorting + $O(E \alpha(V))$ for union-find. Dominant: $O(E \log E) = O(E \log V)$. Simple; works with disconnected graphs (returns minimum spanning FOREST).

Q: Prove Kruskal's algorithm is OPTIMAL via the [cut property].
A: Cut property: for any CUT (partition of vertices), the MINIMUM-WEIGHT edge crossing the cut is in SOME MST. Kruskal picks the cheapest edge connecting two components; this edge crosses the cut between those components — hence is in some MST. Inductive argument: Kruskal's choices can be extended to an MST at each step. Algorithm terminates with a full MST.

## 12.8 Prim's Algorithm

Q: Describe [Prim's MST algorithm].
A: (1) Start with arbitrary vertex $s$; tree $T = \{s\}$. (2) Repeatedly: find the CHEAPEST edge connecting a vertex in $T$ to one NOT in $T$; add it (and the new vertex) to $T$. (3) Stop when $|T| = V$. Use priority queue: for each vertex $v \notin T$, store current min edge weight to $T$. Time: $O(E \log V)$ with binary heap; $O(V \log V + E)$ with Fibonacci heap. Similar to Dijkstra in structure.

Q: When is PRIM PREFERRED over KRUSKAL?
A: Prim dominant on DENSE graphs ($E \approx V^2$): $O(V^2)$ with simple array beats Kruskal's $O(E \log V) = O(V^2 \log V)$. Prim is ALSO simpler when the graph is given in adjacency form without an explicit edge list. Kruskal dominant on SPARSE graphs AND when edges are already sorted. In practice: implement both; choose based on graph density and representation.

## 12.9 The Cut Property and Cycle Property

Q: State and contrast the CUT PROPERTY and CYCLE PROPERTY for MST.
A: [Cut property]: for any cut, the minimum-weight crossing edge is in SOME MST (used to JUSTIFY including edges, as in Prim/Kruskal). [Cycle property]: for any cycle, the MAXIMUM-weight edge is NOT in any MST (used to JUSTIFY excluding edges). These two properties characterize MSTs and prove both Prim and Kruskal optimal. Reverse-delete algorithm uses the cycle property directly.

## 12.10 Borůvka's Algorithm

Q: Describe [Borůvka's algorithm] for MST.
A: In parallel rounds: each connected component finds its CHEAPEST OUTGOING EDGE; add all such edges to the MST simultaneously; merge connected components. Number of components halves each round → $O(\log V)$ rounds. Each round: $O(E)$. Total: $O(E \log V)$. Historically the first MST algorithm (1926). PARALLELIZES NATURALLY (each component independent) — modern interest for distributed/GPU MST.

## 12.11 Single-Source Shortest Path in DAGs

Q: How can you compute shortest paths in a DAG in $O(V + E)$ time?
A: Topologically sort the DAG; relax edges in topological order. Since edges go forward, by the time we process vertex $v$, all its ancestors (and their best paths) have been finalized — simple one-pass relaxation. Works with NEGATIVE weights (no cycles possible). Faster than Bellman-Ford ($O(VE)$) and Dijkstra ($O(E \log V)$). Useful in scheduling (critical path method), compiler dependency analysis.

## 12.12 A Worked Shortest Path

P: Run Dijkstra from vertex A on graph with edges: A-B (4), A-C (1), C-B (2), C-D (5), B-D (1), D-E (3), B-E (8).
S:
**IDENTIFY**: Non-negative weights → Dijkstra works. Trace extraction/relaxation.

**PLAN**: Maintain $dist[]$; priority queue by $dist$. Extract-min, relax outgoing edges.

**EXECUTE**:
Initialize: $dist = \{A: 0, B: \infty, C: \infty, D: \infty, E: \infty\}$. PQ = [(0, A)].

Extract A (dist 0). Relax: $dist[B] = \min(\infty, 0 + 4) = 4$; $dist[C] = \min(\infty, 0 + 1) = 1$. PQ = [(1, C), (4, B)].

Extract C (dist 1). Relax: $dist[B] = \min(4, 1 + 2) = 3$; $dist[D] = \min(\infty, 1 + 5) = 6$. PQ = [(3, B), (4, B stale), (6, D)].

Extract B (dist 3). Relax: $dist[D] = \min(6, 3 + 1) = 4$; $dist[E] = \min(\infty, 3 + 8) = 11$. PQ = [(4, B stale), (4, D), (6, D stale), (11, E)].

Extract B stale (dist 4 but already processed with 3). Skip.

Extract D (dist 4). Relax: $dist[E] = \min(11, 4 + 3) = 7$. PQ = [(6, D stale), (7, E), (11, E stale)].

Extract D stale. Skip.

Extract E (dist 7). No outgoing edges to update.

Final: $dist = \{A: 0, B: 3, C: 1, D: 4, E: 7\}$.

**EVALUATE**: Shortest path from A to E has length 7, route $A \to C \to B \to D \to E$. Notice: the direct edge $B \to E$ (weight 8) is ignored because going through D is cheaper ($3 + 1 + 3 = 7$). Stale entries in the priority queue are skipped lazily — a clean way to handle DECREASE-KEY without implementing it explicitly. Total work: $O((V + E) \log V) = O(7 \log 5) \approx 16$ — small for this hand example, but the method scales to millions of nodes.
