+++
order = 1
subject = "computer-science"
tags = ["cs", "algorithms", "asymptotic-analysis", "big-o", "recurrences", "master-theorem"]
+++

# Algorithms — Asymptotic Analysis and Recurrences

## 1.1 Why Asymptotic Analysis

Q: Why analyze algorithms ASYMPTOTICALLY rather than measuring actual runtime?
A: Actual runtime depends on hardware, compiler, and implementation — all volatile. Asymptotic analysis isolates how runtime GROWS with $n$, giving a portable, implementation-independent comparison (e.g., $O(n \log n)$ sort beats $O(n^2)$ for large $n$ on any machine).

## 1.2 Big-O Notation

C: $f(n) = O(g(n))$ means there exist constants $c > 0, n_0 \geq 0$ such that for all $n \geq n_0$: [$f(n) \leq c \cdot g(n)$].

Q: What does $O(g(n))$ EXPRESS intuitively?
A: An UPPER BOUND on growth — $f$ grows no faster than $g$ up to a constant. Hides constants and lower-order terms ($3n^2 + 100n + 5 = O(n^2)$). Does NOT mean $f$ actually achieves $g$'s rate — it could be much slower.

## 1.3 Omega and Theta

C: [$f(n) = \Omega(g(n))$] means there exist $c, n_0$ with $f(n) \geq c \cdot g(n)$ for all $n \geq n_0$ — a LOWER BOUND on growth.

C: [$f(n) = \Theta(g(n))$] means $f(n) = O(g(n))$ AND $f(n) = \Omega(g(n))$ — TIGHT bound, both upper and lower.

Q: When should you use $\Theta$ vs $O$ vs $\Omega$?
A: Use $\Theta$ when you can PROVE tight matching upper and lower bounds — the strongest, cleanest statement. Use $O$ when you only have an upper bound or when stating worst-case runtime. Use $\Omega$ when proving LOWER bounds (hardness results, information-theoretic arguments: "any comparison-based sorter needs $\Omega(n \log n)$"). "O" is overused in practice when "$\Theta$" would be more informative.

## 1.4 Little-o and Little-omega

C: [$f(n) = o(g(n))$] means $\lim_{n \to \infty} \tfrac{f(n)}{g(n)} = 0$ — $f$ grows STRICTLY slower than $g$.

C: [$f(n) = \omega(g(n))$] means $\lim_{n \to \infty} \tfrac{f(n)}{g(n)} = \infty$ — $f$ grows strictly faster than $g$.

Q: What's the distinction between $O$ and $o$?
A: Big-O allows the ratio to APPROACH a constant (possibly equal to 1); little-o forces the ratio to 0. So $n = O(n)$ but $n \neq o(n)$. Also $n \log n = O(n^2)$ AND $n \log n = o(n^2)$ — little-o is strictly stronger. Similarly for $\Omega$ vs $\omega$. The lowercase versions express "strictly dominates" in growth.

## 1.5 Common Growth Rates

Q: Order the common SUB-LINEAR growth rates from SLOWEST to FASTEST.
A: $1 \ll \log \log n \ll \log n \ll (\log n)^c \ll n^\epsilon \ll \sqrt{n} \ll n$ (for any constant $c > 0$ and any $0 < \epsilon < 1/2$).

Q: Order the common SUPER-LINEAR growth rates from SLOWEST to FASTEST.
A: $n \ll n \log n \ll n^2 \ll n^3 \ll n^k \ll 2^n \ll n! \ll n^n$ (for constant $k > 3$).

Q: Order the growth-rate CLASSES from slowest to fastest.
A: Constant ≪ logarithmic ≪ polylog ≪ polynomial ≪ exponential ≪ factorial. Polynomial algorithms are "efficient"; exponential are "intractable" for all but tiny inputs.

Q: Compare runtime of $n = 10^6$ at different growth rates (nanoseconds per op).
A: $\log_2(10^6) \approx 20$: 20 ns. $n = 10^6$: 1 ms. $n \log n \approx 2 \cdot 10^7$: 20 ms. $n^2 = 10^{12}$: 17 minutes. $n^3 = 10^{18}$: 32 years. $2^n$: cosmic. These numbers drive algorithm selection: $n \log n$ is the practical threshold for "scales to large data"; $n^2$ is fine for $n \leq 10^4$ but painful above; exponential is prohibitive beyond $n \approx 30$.

## 1.6 Recurrences

C: A [recurrence relation] expresses $T(n)$ in terms of $T$ on smaller inputs, plus work done at the current level — e.g., $T(n) = 2 T(n/2) + n$ for mergesort.

Q: What are the THREE main techniques for SOLVING recurrences?
A: (1) [Substitution]: guess a solution and verify by induction. (2) [Recursion tree]: expand the recurrence level by level, sum the work per level. (3) [Master theorem]: directly handle recurrences of the form $T(n) = a T(n/b) + f(n)$. Each has strengths: substitution is general; recursion tree builds intuition; master theorem is plug-and-chug for common cases.

## 1.7 The Master Theorem

Q: Before learning the master theorem, predict: for $T(n) = 2T(n/2) + n$ (mergesort) vs $T(n) = 2T(n/2) + n^2$, which has bigger asymptotic runtime and why?
A: The second. Heavier per-level work ($n^2$ vs $n$) dominates. Mergesort is $\Theta(n \log n)$; the second collapses to $\Theta(n^2)$ because the top level alone is $n^2$ and lower levels shrink geometrically.

Q: You see a recurrence of the form $T(n) = a T(n/b) + f(n)$. What's your first move?
A: Compute $c = \log_b a$, then compare $f(n)$ to $n^c$ — that comparison decides which master theorem case applies.

Q: State the [master theorem] for recurrences $T(n) = a T(n/b) + f(n)$ with $a \geq 1, b > 1$.
A: Let $c = \log_b a$. (1) If $f(n) = O(n^{c - \epsilon})$ for some $\epsilon > 0$: $T(n) = \Theta(n^c)$. (2) If $f(n) = \Theta(n^c \log^k n)$: $T(n) = \Theta(n^c \log^{k+1} n)$. (3) If $f(n) = \Omega(n^{c + \epsilon})$ AND regularity condition ($a f(n/b) \leq k f(n)$ for some $k < 1$): $T(n) = \Theta(f(n))$. The three cases: recursion dominates, recursion and split balance, split dominates.

Q: Apply master theorem to MERGESORT's recurrence $T(n) = 2T(n/2) + \Theta(n)$.
A: $a = 2, b = 2$ so $c = \log_2 2 = 1$. $f(n) = \Theta(n) = \Theta(n^1) = \Theta(n^c \log^0 n)$ — Case 2 with $k = 0$. So $T(n) = \Theta(n^1 \log^1 n) = \Theta(n \log n)$. Confirms mergesort is $\Theta(n \log n)$. The master theorem handles this in one line; substitution would need explicit induction.

## 1.8 Recursion Trees

Q: When is RECURSION TREE analysis preferable to master theorem?
A: When the recurrence DOESN'T fit the master theorem form (e.g., $T(n) = T(n/3) + T(2n/3) + n$, unequal splits), or when you want to BUILD INTUITION about work distribution across levels. Draw the tree, label each node with its work, sum by level, multiply by number of levels. For $T(n) = T(n/3) + T(2n/3) + n$: work per level is $n$ at every level from root to the deepest leaf; depth $\log_{3/2} n$; total $\Theta(n \log n)$.

## 1.9 Divide-and-Conquer Recurrences

Q: What's the INTUITIVE interpretation of master theorem cases?
A: Each level does $f(n)$ work combining; recursion tree has $a^{\log_b n} = n^c$ leaves at bottom. (1) Leaves dominate (exponential growth in leaves exceeds per-level combining): $\Theta(n^c)$. (2) All levels contribute equally: multiply leaf work by $\log n$. (3) Combining dominates (root/top levels exceed leaf work): $\Theta(f(n))$. Recognizing which case applies = comparing $f(n)$ to $n^c$.

## 1.10 Substitution Method

Q: Describe the [substitution method] for solving recurrences.
A: (1) GUESS the asymptotic form of the solution. (2) Assume it holds for smaller $n$ (inductive hypothesis). (3) Plug into the recurrence; verify the hypothesis for larger $n$; deduce constants. Most versatile method: works for any recurrence. Pitfall: choosing an asymptotically correct but constant-off guess gives a non-inductive proof — subtract a lower-order term from the guess when needed ("strengthening the hypothesis").

## 1.11 Amortized Analysis Preview

Q: When is WORST-CASE per-operation analysis too pessimistic?
A: When a DATA STRUCTURE has occasional expensive operations balanced by many cheap ones — e.g., dynamic array doubling. Single insert is $O(1)$ amortized but $O(n)$ worst-case (when resize triggers). AMORTIZED analysis averages cost OVER A SEQUENCE of operations, giving a TIGHTER bound on TOTAL cost. Techniques: aggregate (sum and divide), accounting (credit scheme), potential function. Covered in depth in chapter 14.

## 1.12 A Worked Recurrence

P: Solve the recurrence $T(n) = 3 T(n/4) + n \log n$ using the master theorem.
S:
**IDENTIFY**: Standard divide-and-conquer recurrence with unequal $a, b$. Apply master theorem.

**PLAN**: Compute $c = \log_b a$; compare $f(n)$ to $n^c$.

**EXECUTE**:
- $a = 3, b = 4$. So $c = \log_4 3 \approx 0.79$.
- $f(n) = n \log n = \Theta(n^1 \log n)$.
- Compare $f(n)$ to $n^c = n^{0.79}$: since $n^1 \log n$ grows STRICTLY faster than $n^{0.79 + \epsilon}$ for any $0 < \epsilon < 0.21$, we're in CASE 3.
- Regularity: $a f(n/b) = 3 \cdot (n/4) \log(n/4) = (3n/4)(\log n - 2) \leq (3/4) \cdot n \log n = (3/4) f(n)$ for $n$ large enough. $k = 3/4 < 1$ ✓.
- Conclusion: $T(n) = \Theta(f(n)) = \Theta(n \log n)$.

**EVALUATE**: The combining work $n \log n$ dominates the recursion work. At each level, the total work shrinks by a factor of $3/4$ (since 3 subproblems of size $n/4$ have total combining cost $\tfrac{3}{4} n \log(n/4)$ at that level). Geometric series converges; total work = $\Theta$ of top-level work = $\Theta(n \log n)$. Sanity check: if $f(n) = n^2$, we'd get $\Theta(n^2)$ by the same argument; if $f(n) = 1$, case 1 gives $\Theta(n^{0.79})$. Master theorem unifies all these with one formula.
