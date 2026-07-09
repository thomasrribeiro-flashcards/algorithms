+++
order = 11
subject = "Computer Science"
tags = ["cs", "algorithms", "graph", "bfs", "dfs", "topological-sort", "scc", "traversal"]
+++

# Algorithms — Graph Traversal

## 11.1 Graph Representations

C: A graph $G = (V, E)$ consists of a set of [vertices] $V$ and a set of [edges] $E \subseteq V \times V$ (directed) or $\{V \text{ pairs}\}$ (undirected).

Q: Compare ADJACENCY LIST vs ADJACENCY MATRIX representations.
A: Adjacency list: for each vertex $v$, a list of neighbors. Space $O(V + E)$. Iteration over neighbors of $v$ is $O(\deg(v))$. Best for SPARSE graphs. Adjacency matrix: $V \times V$ binary matrix $M[i][j] = 1$ iff $(i, j) \in E$. Space $O(V^2)$. Edge lookup is $O(1)$. Best for DENSE graphs. Most real-world graphs are sparse — adjacency list is the default.

## 11.2 Breadth-First Search

Q: Describe [breadth-first search (BFS)].
A: Given a start vertex $s$: visit vertices in order of INCREASING DISTANCE (fewest edges from $s$). Uses a QUEUE. (1) Enqueue $s$; mark visited. (2) While queue non-empty: dequeue $v$; for each unvisited neighbor $u$ of $v$, mark $u$, record $\text{parent}(u) = v$ and $\text{dist}(u) = \text{dist}(v) + 1$, enqueue $u$. Time: $O(V + E)$ with adjacency list. Computes shortest paths in UNWEIGHTED graphs.

Q: What does BFS compute directly from a source vertex $s$?
A: (1) [Shortest paths] from $s$ in unweighted graphs (number of edges). (2) [REACHABILITY]: all vertices reachable from $s$. Restarted from each unvisited vertex, it also finds [CONNECTED COMPONENTS] in undirected graphs.

Q: Name two algorithms that use BFS as a building block.
A: (1) [BIPARTITENESS] check: 2-color vertices by BFS layer parity. (2) [Edmonds-Karp] max-flow: BFS finds shortest augmenting paths.

## 11.3 Depth-First Search

Q: Describe [depth-first search (DFS)].
A: Uses a STACK (or recursion). (1) Start at $s$. Mark $s$ visited. (2) For each unvisited neighbor $u$: recursively DFS from $u$. (3) When finished, mark $s$ as "closed." Time: $O(V + E)$. Unlike BFS, explores DEEPLY before backtracking. Produces a [DFS tree] and classifies edges as tree, back, forward, or cross edges — rich structural information.

Q: What graph properties does DFS reveal?
A: (1) [Cycle detection]: BACK EDGE in DFS ↔ cycle. (2) [TOPOLOGICAL SORT] of a DAG (via DFS finish times). (3) [STRONGLY CONNECTED COMPONENTS] (Tarjan, Kosaraju). (4) [BICONNECTED COMPONENTS] and ARTICULATION POINTS. (5) [PATHS and CONNECTIVITY]. The discovery/finish time structure classifies edges and exposes depth-first-specific properties.

## 11.4 DFS Edge Classification

Q: In a DFS tree of a DIRECTED graph, how are edges classified?
A: (1) [Tree edge]: $(u, v)$ where $v$ is discovered by exploring $(u, v)$. (2) [Back edge]: $v$ is an ANCESTOR of $u$ in the DFS tree. Back edges → cycles. (3) [Forward edge]: $v$ is a proper DESCENDANT of $u$ but NOT via tree edge. (4) [Cross edge]: $u$ and $v$ have no ancestor relationship. In UNDIRECTED DFS, edges are only tree or back (forward and cross don't arise by symmetry).

## 11.5 Topological Sort

Q: Describe [topological sort] of a DAG (directed acyclic graph).
A: A LINEAR ORDERING of vertices such that for every edge $(u, v)$: $u$ appears BEFORE $v$. Exists iff the graph is acyclic. Two algorithms: (1) [DFS-based]: run DFS; output vertices in REVERSE POSTORDER (by decreasing finish time). (2) [Kahn's]: repeatedly remove a vertex with in-degree 0; output it; decrement neighbors' in-degrees. Both $O(V + E)$. Uses: course scheduling, build dependency resolution, Excel formula evaluation.

Q: Prove DFS reverse-postorder gives a valid topological sort.
A: For any edge $(u, v)$: consider DFS finish times $f(u), f(v)$. Case 1: when $u$'s DFS starts, $v$ is unvisited — $v$ will be explored via $(u, v)$ (or later), finishing BEFORE $u$. So $f(v) < f(u)$. Case 2: $v$ is already in progress when we try to traverse $(u, v)$ — but then $v$ is an ANCESTOR of $u$, making $(u, v)$ a back edge, contradicting DAG. Hence every edge has $f(v) < f(u)$, meaning $u$ appears earlier in reverse-finish order ✓.

## 11.6 Strongly Connected Components

Q: What is a [strongly connected component (SCC)] in a directed graph?
A: A MAXIMAL SET of vertices where every pair is MUTUALLY REACHABLE (there are paths from $u$ to $v$ AND from $v$ to $u$). SCCs partition the vertices. CONDENSATION: contract each SCC to a single node; result is a DAG. Useful in: compiler optimization (loops = non-trivial SCCs in control flow), 2-SAT, social network analysis.

Q: Describe [Tarjan's algorithm] for computing SCCs.
A: Single DFS with TWO markers per vertex: discovery time $disc[v]$ and $low[v]$ = earliest ancestor reachable from $v$'s subtree via back/cross edges. Maintain stack of active (unfinished) vertices. When DFS finishes $v$ with $disc[v] = low[v]$: $v$ is the "root" of an SCC; pop stack until $v$ is popped — those form one SCC. $O(V + E)$ time, single pass.

Q: Describe [Kosaraju's algorithm] for computing SCCs.
A: (1) Run DFS on $G$; push vertices onto stack as they FINISH. (2) Compute the TRANSPOSE $G^T$ (reverse all edges). (3) Run DFS on $G^T$ in order of the stack (top first). Each tree in this second DFS is an SCC. Correctness: a vertex with the LATEST finish in $G$ is in a "source" SCC of the condensation; DFS in $G^T$ from it discovers only that SCC. $O(V + E)$ time; TWO PASSES but simpler than Tarjan.

## 11.7 Articulation Points and Bridges

C: An [articulation point] (cut vertex) in a connected undirected graph is a vertex whose removal DISCONNECTS the graph. A [bridge] is an edge whose removal disconnects.

Q: How does DFS detect articulation points and bridges?
A: During DFS, for each vertex $v$ compute $low[v]$ = minimum discovery time reachable from $v$'s subtree. (1) ROOT is articulation iff it has $\geq 2$ tree children. (2) NON-ROOT $v$ is articulation iff it has a child $u$ with $low[u] \geq disc[v]$. (3) Edge $(v, u)$ is a bridge iff $low[u] > disc[v]$. $O(V + E)$ time. Important for network reliability analysis: articulation points/bridges are single failure points.

## 11.8 Eulerian Paths and Circuits

Q: When does a graph have an [Eulerian circuit] (a cycle visiting every EDGE exactly once)?
A: Undirected: iff the graph is connected and EVERY vertex has EVEN degree. Directed: iff strongly connected (restricted to vertices with edges) and every vertex has $\text{in-degree} = \text{out-degree}$. [Eulerian path] (every edge once, not necessarily closed): undirected needs exactly 0 or 2 vertices of odd degree; directed needs at most one "source" and "sink" with specific degree imbalance. [Hierholzer's algorithm] finds one in $O(V + E)$.

Q: Contrast EULERIAN vs HAMILTONIAN problems.
A: Eulerian (visit every EDGE once): POLYNOMIAL time — simple degree check + linear construction. Hamiltonian (visit every VERTEX once): NP-COMPLETE. Stark asymmetry: visit every bridge easy; visit every city hard. Euler's 1736 solution of the Königsberg problem is foundational; Hamiltonian cycle remains the canonical hard problem (TSP is weighted Hamiltonian).

## 11.9 Bipartiteness Check

Q: How does BFS check if an undirected graph is BIPARTITE?
A: Bipartite = 2-colorable (vertices split into two sets with all edges BETWEEN sets). BFS from any vertex: color by LAYER PARITY (even-depth → color A, odd-depth → color B). Check every edge: if both endpoints same color, graph is NOT bipartite. $O(V + E)$ time. Odd cycles are exactly the obstruction to bipartiteness (König's theorem).

## 11.10 Graph Representations for Special Structures

Q: What specialized graph representations exist beyond adjacency list/matrix?
A: (1) [COMPRESSED SPARSE ROW (CSR)]: two arrays for efficient iteration, used in GPU/HPC graph algorithms. (2) [Edge list]: list of $(u, v, w)$ triples — simplest, good for static graphs. (3) [Implicit graphs]: grid graphs, state graphs, infinite graphs where nodes/edges are generated on demand (used in AI search, pathfinding). (4) Incidence matrix, adjacency structure for multigraphs. Choose based on: density, mutability, operations needed.

## 11.11 Parallel and Distributed Graph Traversal

Q: How does BFS PARALLELIZE?
A: Process each BFS LAYER in parallel: all vertices at distance $d$ can be expanded simultaneously. Synchronize between layers. Scales well on shared-memory multi-core AND distributed clusters (MPI). Practical: Pregel / Giraph / GraphX model — "vertex-centric" computation, each vertex processes messages from neighbors per round. Used for web-scale graph analytics (PageRank, connected components on billion-edge graphs).

## 11.12 A Worked DFS Example

P: Run DFS on this directed graph starting from A: $A \to B, A \to C, B \to D, C \to D, D \to E, E \to C$. Classify each edge as tree, back, forward, or cross. Output topological order (if DAG).
S:
**IDENTIFY**: Directed graph; DFS discovery/finish times.

**PLAN**: Perform DFS recursively, tracking discovery and finish times.

**EXECUTE**:
- Start: DFS(A). $disc[A] = 1$.
  - Visit B (neighbor). TREE EDGE $A \to B$. $disc[B] = 2$.
    - Visit D. TREE EDGE $B \to D$. $disc[D] = 3$.
      - Visit E. TREE EDGE $D \to E$. $disc[E] = 4$.
        - Visit C. TREE EDGE $E \to C$. $disc[C] = 5$.
          - Neighbor D: already discovered, still in progress → BACK EDGE $C \to D$.
          - C finishes. $fin[C] = 6$.
        - E finishes. $fin[E] = 7$.
      - D finishes. $fin[D] = 8$.
    - B finishes. $fin[B] = 9$.
  - Back in A's DFS: neighbor C: already finished → CROSS (or FORWARD, depending on order of traversal). Actually C is a DESCENDANT of A via the DFS tree ($A \to B \to D \to E \to C$), so $A \to C$ is a FORWARD EDGE.
  - A finishes. $fin[A] = 10$.

Edge classification:
- Tree: $A \to B, B \to D, D \to E, E \to C$.
- Back: $C \to D$.
- Forward: $A \to C$.
- Cross: none.

Topological sort: since there's a BACK EDGE, graph has a CYCLE — NOT a DAG. No topological ordering exists. The cycle: $D \to E \to C \to D$.

**EVALUATE**: Back edge ($C \to D$) correctly reveals the cycle $D \to E \to C \to D$. If we removed edge $C \to D$, the graph would be a DAG, and reverse-finish order $(A, B, D, E, C)$ would be a valid topological sort. DFS's classification of edges and discovery/finish times give rich structural info in ONE linear-time pass — foundation for SCC, biconnectivity, many more.
