+++
order = 13
subject = "computer-science"
tags = ["cs", "algorithms", "network-flows", "max-flow", "min-cut", "ford-fulkerson", "bipartite-matching", "edmonds-karp"]
+++

# Algorithms — Network Flows and Matching

## 13.1 Flow Networks

C: A [flow network] is a DIRECTED graph $G = (V, E)$ with a non-negative CAPACITY $c(u, v) \geq 0$ on each edge, a distinguished SOURCE $s$, and a distinguished SINK $t$.

C: A flow $f$ assigns $f(u, v) \geq 0$ to each edge subject to (i) [capacity]: $f(u, v) \leq c(u, v)$, and (ii) [conservation]: for every $v \neq s, t$, $\sum_u f(u, v) = \sum_w f(v, w)$ (flow in = flow out).

## 13.2 The Maximum Flow Problem

Q: What is the [max-flow problem]?
A: Find a flow with MAXIMUM VALUE $|f| = \sum_v f(s, v) - \sum_v f(v, s)$ (net flow out of source). Models: (1) shipping goods through a logistics network; (2) packet routing; (3) bipartite matching (unit capacities); (4) project selection, baseball elimination. Foundational combinatorial optimization problem.

## 13.3 The Ford-Fulkerson Method

Q: Describe the [Ford-Fulkerson] augmenting-path method.
A: (1) Start with zero flow. (2) While an AUGMENTING PATH exists in the residual graph: (a) find the path; (b) push flow equal to the bottleneck (min residual capacity on path); (c) update residual graph. (3) Terminate when no augmenting path remains. Correctness: max-flow min-cut theorem. Runtime depends on HOW augmenting paths are chosen.

Q: What is the [residual graph]?
A: Constructed from current flow $f$. For each edge $(u, v)$ with capacity $c$ and flow $f(u, v)$: add FORWARD edge with residual capacity $c - f(u, v)$, and BACKWARD edge with capacity $f(u, v)$ (to CANCEL flow). Augmenting paths in the residual graph correspond to flow increases. Backward edges are crucial: they allow REROUTING of previous (suboptimal) flow decisions.

## 13.4 Max-Flow Min-Cut Theorem

Q: State the [max-flow min-cut theorem].
A: In any flow network: MAX flow value EQUALS MIN capacity of any $s$-$t$ CUT (partition of $V$ into $S \ni s$ and $T \ni t$; cut capacity = sum of capacities from $S$ to $T$). Proof: (i) max-flow ≤ min-cut by construction (every unit of flow crosses every cut at least once). (ii) At termination of Ford-Fulkerson, the set of vertices reachable from $s$ in residual graph defines a cut whose capacity = flow value. Classical min-max duality result.

## 13.5 Edmonds-Karp

Q: Why does Ford-Fulkerson's WORST-CASE runtime depend on path choice?
A: With BAD path choice, Ford-Fulkerson can take $O(|f^*| \cdot E)$ time — pseudopolynomial (depends on flow value, not input size). Worst case: ping-pong augmentation around a cycle of low-capacity edges. Can even fail to terminate with IRRATIONAL capacities. [Edmonds-Karp]: always use BFS (shortest augmenting path); runtime becomes $O(V E^2)$ — STRONGLY polynomial, works regardless of capacities.

Q: Why does BFS-augmented Ford-Fulkerson (Edmonds-Karp) terminate in $O(VE^2)$?
A: Key lemma: the shortest-path distance from $s$ to $t$ in the residual graph NEVER DECREASES as augmentations proceed. Each augmentation "saturates" at least one edge; a saturated edge only reappears after the distance from $s$ to its tail increases by 2 — happening at most $V/2$ times per edge. So total augmentations ≤ $O(VE)$; each takes $O(E)$ for BFS. Total: $O(VE^2)$.

## 13.6 Push-Relabel

Q: Describe the [push-relabel] max-flow algorithm.
A: Maintains a PREFLOW (flow with possible excess at some nodes). Each node has a HEIGHT label; operations: (1) PUSH excess from a higher-height node to a lower-height neighbor. (2) RELABEL (increase height) when no pushable neighbor exists. Eventually, excess drains to $s$ or $t$. Best variant (highest-height selection): $O(V^2 \sqrt{E})$. Faster than Edmonds-Karp in PRACTICE, especially on dense graphs.

## 13.7 Bipartite Matching

Q: How does max-flow solve [bipartite maximum matching]?
A: Given bipartite graph $G = (L \cup R, E)$: build a flow network with source $s$ connected to $L$ (unit capacity edges), sink $t$ connected from $R$ (unit capacity), $L$-to-$R$ edges of $G$ with unit capacity. Max flow value = max matching size. Intuitively: each unit of flow traces a matched pair. Hopcroft-Karp algorithm: $O(E \sqrt{V})$ — faster than generic Edmonds-Karp.

Q: What is [König's theorem]?
A: In any bipartite graph: the SIZE OF MAX MATCHING EQUALS the SIZE OF MIN VERTEX COVER. Proof via max-flow min-cut on the bipartite-matching flow network. Consequence: both max-matching and min-vertex-cover are POLYNOMIAL in bipartite graphs (via flow), despite min-vertex-cover being NP-HARD on general graphs. Bipartiteness saves the day.

## 13.8 Applications of Max-Flow

Q: Name FIVE applications of max-flow modeling.
A: (1) [Image segmentation]: foreground/background separation via min-cut. (2) [Baseball elimination]: can team $X$ still win the pennant? Reduces to max-flow feasibility. (3) [Airline scheduling]: match flights to crews/aircraft as bipartite matching. (4) [Project selection]: projects with prerequisites and profits → min-cut. (5) [Sports brackets, task assignment, routing, VLSI placement]. The pattern: whenever "matching with capacity constraints" or "find a bottleneck" arises, max-flow applies.

## 13.9 Minimum-Cost Flow

Q: What is the [minimum-cost flow] problem?
A: Given capacities $c(u, v)$ and per-unit COSTS $\text{cost}(u, v)$: find a flow of value $F$ (given) minimizing $\sum f(u, v) \cdot \text{cost}(u, v)$. Generalizes max-flow (constant cost) and shortest path (unit flow). Solvable in polynomial time via: (1) [cycle-canceling] (find and cancel negative-cost cycles in residual graph), (2) [successive shortest paths] (push flow along cheapest augmenting path), (3) [network simplex]. Models: assignment problems, transportation, supply chain.

## 13.10 Multi-Commodity Flow

Q: What is [multi-commodity flow]?
A: Multiple source-sink pairs $(s_1, t_1), (s_2, t_2), \ldots$ each sending their own commodity through the network; edges have shared capacity; goal varies (maximize total flow, minimize cost, check feasibility). DECISION VERSION of integer multi-commodity flow is NP-HARD; fractional (LP relaxation) is polynomial. Models real networks: internet routing, logistics with multiple products, traffic engineering.

## 13.11 The Integrality Theorem

Q: State the [integrality theorem] for flows.
A: If all capacities are INTEGERS, there exists an INTEGER max-flow. Proof: Ford-Fulkerson with integer capacities augments by integer amounts each iteration — final flow is integer. Corollary: matching and other combinatorial reductions automatically yield integer solutions. This is why the bipartite-matching-as-flow reduction works: we get a "matching" (not fractional) directly from integer max-flow.

## 13.12 A Worked Max-Flow

P: Find the max flow from $s$ to $t$ in the network: $s \to a$ (cap 10), $s \to b$ (5), $a \to b$ (15), $a \to t$ (10), $b \to t$ (10).
S:
**IDENTIFY**: 4 nodes, 5 edges. Use Ford-Fulkerson with BFS (Edmonds-Karp).

**PLAN**: Find augmenting paths; push flow; repeat.

**EXECUTE**:
Round 1: BFS from $s$ to $t$. Shortest path: $s \to a \to t$ (length 2). Bottleneck: $\min(10, 10) = 10$. Push 10. Flow: $s$-$a$: 10, $a$-$t$: 10.

Residual: $s$-$a$ forward 0, backward 10. $a$-$t$ forward 0, backward 10. $s$-$b$ forward 5, $a$-$b$ forward 15, $b$-$t$ forward 10.

Round 2: BFS. Path: $s \to b \to t$. Bottleneck: $\min(5, 10) = 5$. Push 5. Flow: $s$-$b$: 5, $b$-$t$: 5.

Residual: $s$-$b$ forward 0, backward 5. $b$-$t$ forward 5.

Round 3: BFS. Look for augmenting path. $s \to a$ saturated (residual 0). $s \to b$ saturated. Any path must include a backward edge. Backward $a$-$s$ has capacity 10, but $s$ is source — no use. Backward $t$-$a$ has 10 but $t$ is sink.

Is there a path $s \to b \to a$... $s \to b$ residual is 0. No augmenting path exists.

Max flow = 10 + 5 = 15.

Verify via min-cut: cut $S = \{s\}, T = \{a, b, t\}$ has capacity $10 + 5 = 15$. Matches ✓.

**EVALUATE**: Max flow 15; min cut also 15 — confirming max-flow min-cut. In this simple case, Ford-Fulkerson terminates quickly. For larger networks, efficient implementations (Edmonds-Karp or push-relabel) handle thousands of vertices in milliseconds. Notice how max-flow cleanly decomposes into two edge-disjoint "paths" carrying 10 and 5 units — this decomposition is a consequence of the integrality theorem.
