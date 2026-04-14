+++
order = 14
subject = "Computer Science"
tags = ["cs", "algorithms", "randomized", "amortized", "las-vegas", "monte-carlo", "potential-function"]
+++

# Algorithms — Randomized Algorithms and Amortized Analysis

## 14.1 Why Randomization

Q: Why use RANDOMIZATION in algorithm design?
A: (1) SIMPLICITY: randomized quicksort is simpler to state and analyze than deterministic $O(n \log n)$ selection pivots. (2) SPEED: often faster than deterministic counterparts. (3) ROBUSTNESS: defeats adversarial inputs — no worst-case pattern exists. (4) DISTRIBUTED/PARALLEL: no coordination needed (symmetry-breaking). (5) SOMETIMES ONLY SOLUTION: primality testing, hashing in practice. Randomization adds a tool that deterministic methods cannot match.

## 14.2 Las Vegas vs Monte Carlo

Q: Distinguish [Las Vegas] and [Monte Carlo] randomized algorithms.
A: Las Vegas: always produces CORRECT output; runtime is a RANDOM variable (may be long). Examples: randomized quicksort, quickselect. Monte Carlo: runtime is bounded; output is PROBABILISTICALLY correct (small chance of error). Examples: Miller-Rabin primality test, Freivalds' matrix-multiplication verification. Trade: Las Vegas gives certainty; Monte Carlo gives hard runtime bounds. Monte Carlo errors often reducible by running multiple times (boost confidence).

## 14.3 Randomized Quicksort

Q: Why does RANDOMIZED QUICKSORT achieve $O(n \log n)$ EXPECTED time?
A: By picking a UNIFORMLY RANDOM pivot, the expected position of the pivot is the MEDIAN RANK. Expected partition imbalance is controlled: with probability $\geq 1/2$, pivot is in the middle half, giving balanced split. Formal analysis: expected number of comparisons $= 2n \ln n + O(n)$. Crucially, NO INPUT CAN FORCE WORST CASE — the adversary cannot know the random coin flips in advance.

## 14.4 Randomized Algorithms for Primality

Q: Describe [Miller-Rabin primality test].
A: To test if $n$ is prime: write $n - 1 = 2^s \cdot d$ (odd $d$). Pick random witness $a \in [2, n-2]$. Check if $a^d \equiv 1$ or $a^{2^r d} \equiv -1 \pmod n$ for some $0 \leq r < s$. If yes: "probably prime." If no: DEFINITELY composite. For composite $n$: at least $3/4$ of witnesses catch it. Repeat $k$ times: error $\leq 4^{-k}$. Used in RSA key generation, SSL, crypto libraries. AKS (2002) is deterministic polynomial but slower in practice.

## 14.5 Hashing and Universal Hashing

Q: How does [universal hashing] use randomization to defeat adversaries?
A: A family $\mathcal{H}$ of hash functions is universal if for any two distinct keys $x \neq y$: $P_{h \sim \mathcal{H}}[h(x) = h(y)] \leq 1/m$. At hash-table creation, pick $h$ UNIFORMLY at random from $\mathcal{H}$. Any adversary, not knowing the chosen $h$, cannot craft collisions — expected $O(1)$ operations. Simple example: $h_{a, b}(k) = ((ak + b) \mod p) \mod m$ for random $a, b$. Deployed in PRODUCTION hash tables (SipHash in Python, Rust).

## 14.6 Randomized Graph Algorithms

Q: Describe [Karger's min-cut algorithm].
A: To find a minimum cut: repeatedly CONTRACT a random edge (merge endpoints) until 2 vertices remain. The edges between them form a candidate min-cut. Probability of finding a specific min-cut: $\geq 1/\binom{n}{2} \geq 2/n^2$. Repeat $O(n^2 \log n)$ times → $O(n^4 \log n)$ algorithm finding min-cut with high probability. Simple, elegant, entirely different from max-flow-based deterministic approaches. Improvement (Karger-Stein): $O(n^2 \log^3 n)$.

Q: Why is KARGER'S algorithm SURPRISING?
A: Because the naive "contract random edge" feels TOO LAZY — it ignores the structure! Yet by amplification (running $O(n^2)$ times), you find the min-cut with high probability. Demonstrates that RANDOMIZATION + REPETITION can substitute for clever deterministic structure. Similar "run-many-times" style: randomized primality, Freivalds' verification. Power of the union bound + low per-trial success probability.

## 14.7 Amortized Analysis

Q: Why use AMORTIZED analysis instead of worst-case per operation?
A: Because many data structures have OCCASIONAL expensive operations BALANCED by many cheap ones — per-op worst case is misleadingly pessimistic. Amortized gives the AVERAGE cost OVER A SEQUENCE of operations. Example: dynamic array insert is $O(1)$ amortized (doubling) but $O(n)$ worst-case. Amortized analysis proves the SEQUENCE has low total cost, even if individual ops spike.

## 14.8 Aggregate Method

Q: Describe the [aggregate method] of amortized analysis.
A: Compute TOTAL cost $T(n)$ for a WORST-CASE sequence of $n$ operations. Amortized cost per operation = $T(n) / n$. Simple but crude — gives the same amortized cost to every operation. Example: $n$ inserts into a dynamic array. Total work: $n$ inserts + $\leq 2n$ copies during resizes = $O(n)$. Amortized per insert: $O(1)$.

## 14.9 Accounting Method

Q: Describe the [accounting method] of amortized analysis.
A: Assign each operation an AMORTIZED COST (a "charge"). Save excess charges as CREDITS on the data structure. When an expensive op occurs, use accumulated credits to pay for it. VALID if total credit never goes negative. More flexible than aggregate — different operations can have different amortized costs. Example: charge 3 per dynamic-array insert; 1 pays for the insert itself, 2 are saved to pay for future copies during resize.

## 14.10 Potential Method

Q: Describe the [potential method] of amortized analysis.
A: Define POTENTIAL function $\Phi(D_i) \geq 0$ measuring "stored energy" of the data structure after $i$ operations. Amortized cost of op $i$: $\hat{c}_i = c_i + \Phi(D_i) - \Phi(D_{i-1})$. Total amortized cost $\geq$ total actual cost (since $\Phi \geq 0$). MOST FLEXIBLE method; used for complex structures (Fibonacci heaps, splay trees). Example: $\Phi(\text{dynamic array}) = 2 \cdot (\text{size}) - (\text{capacity})$; resize pays down potential accumulated from inserts.

## 14.11 Amortized Analysis of Splay Trees

Q: What are [splay trees], and what's their amortized guarantee?
A: Self-adjusting binary search trees. After each access, SPLAY the accessed node to the root via rotations (restructuring the tree "on the way up"). Amortized $O(\log n)$ per operation (access, insert, delete) — despite possibly $O(n)$ worst-case individual ops. Analyzed via potential method. Remarkable "working set" theorem: frequently-accessed nodes stay near the root. No rank/color annotations; purely structural. Widely used in Unix `ioctl`, Windows Kernel.

## 14.12 A Worked Amortized Analysis

P: Analyze dynamic array INSERT using the POTENTIAL method.
S:
**IDENTIFY**: Dynamic array with doubling: capacity $c$, size $s$. Insert is $O(1)$ when $s < c$; $O(s)$ when $s = c$ (resize).

**PLAN**: Define potential so that amortized cost is $O(1)$ per insert. Need $\Phi$ high just before a resize (to "pay" for it) and $\Phi = 0$ just after.

**EXECUTE**:
Define $\Phi = 2s - c$ when $s \geq c/2$; else $\Phi = 0$. (Standard choice.)

Case 1: Insert without resize ($s < c$ before insert, $s + 1 \leq c$ after).
Actual cost: 1. Change in potential: new $\Phi - $ old $\Phi$. After: $s' = s + 1, c' = c$. If $s + 1 \geq c/2$, $\Phi' = 2(s+1) - c = 2s - c + 2$. $\Delta \Phi = 2$ (or less, depending on boundary). Amortized: $1 + 2 = 3$.

Case 2: Insert with resize (when $s = c$). Copy $c$ old elements + 1 new = $c + 1$ operations.
Before insert: $\Phi = 2c - c = c$. After: $s' = c + 1, c' = 2c, \Phi' = 2(c+1) - 2c = 2$.
$\Delta \Phi = 2 - c$. Amortized: $(c + 1) + (2 - c) = 3$.

Both cases: amortized cost $\leq 3 = O(1)$. Total cost over $n$ inserts: $\leq 3n = O(n)$, so insertion is $O(1)$ AMORTIZED.

**EVALUATE**: The potential function brilliantly "stores" credits during cheap inserts and releases them during expensive resizes. Each insert pays 3 (its own cost + 2 saved); each resize consumes $c$ saved credits. Without the potential, resize would look like a $O(n)$ spike; WITH it, we see the true average. Similar analyses prove amortized bounds for binomial heaps (lazy merge), Fibonacci heaps (decrease-key), splay trees (rotation amortization).
