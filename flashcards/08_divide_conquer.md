+++
order = 8
subject = "Computer Science"
tags = ["cs", "algorithms", "divide-and-conquer", "recursion", "strassen", "closest-pair", "fft"]
+++

# Algorithms — Divide and Conquer

## 8.1 The Divide-and-Conquer Paradigm

Q: What are the THREE steps of the divide-and-conquer paradigm?
A: (1) [DIVIDE] the problem into subproblems of the same type, typically smaller. (2) [CONQUER] the subproblems recursively (base case: solve directly). (3) [COMBINE] subproblem solutions into a solution for the original problem. Examples: mergesort (split, sort, merge), quicksort (partition, sort each half), binary search (halve, recurse). Natural fit for recurrences $T(n) = a T(n/b) + f(n)$, solvable by master theorem.

## 8.2 Why Divide and Conquer Works

Q: Why does divide-and-conquer OFTEN yield better algorithms than iterative approaches?
A: Because TWO half-size problems TOGETHER often have LESS work than one full-size problem, IF subproblems' answers can be combined in less-than-full time. Example: naïve pairwise comparison of $n$ points is $O(n^2)$; divide-and-conquer closest-pair is $O(n \log n)$ because the combining step only needs to consider a thin "strip" near the dividing line. Asymmetry: good combining → good algorithm.

## 8.3 Binary Search

Q: Describe [binary search] as a divide-and-conquer algorithm.
A: Given a SORTED array and target $t$: compare $t$ with middle element $A[m]$. If equal, done. If $t < A[m]$, recurse on left half; else recurse on right half. Base case: empty range → not found. Time: $T(n) = T(n/2) + O(1) = O(\log n)$. Space: $O(\log n)$ recursive (or $O(1)$ iterative). Fundamental: any sorted/monotone problem invites binary search.

## 8.4 Mergesort Revisited

Q: Recap mergesort in divide-and-conquer terms.
A: Divide: split array in half ($O(1)$). Conquer: recursively mergesort each half. Combine: MERGE two sorted halves into one in $O(n)$. Recurrence: $T(n) = 2T(n/2) + O(n) = O(n \log n)$ (master theorem Case 2). The combine step costs linear time; recursion halves the problem. Same pattern appears in many algorithms — recognize it and apply master theorem.

## 8.5 Karatsuba Multiplication

Q: Describe [Karatsuba's algorithm] for multiplying two $n$-digit integers.
A: Naïve: $O(n^2)$ digit multiplications. Karatsuba: split each number as $x = x_1 \cdot B^{n/2} + x_0$ (high/low halves). Observation: $xy = x_1 y_1 B^n + (x_1 y_0 + x_0 y_1) B^{n/2} + x_0 y_0$. NAIVELY needs 4 multiplications of $n/2$-digit numbers. TRICK: $x_1 y_0 + x_0 y_1 = (x_1 + x_0)(y_1 + y_0) - x_1 y_1 - x_0 y_0$. Now only 3 multiplications needed. Recurrence: $T(n) = 3 T(n/2) + O(n) = O(n^{\log_2 3}) \approx O(n^{1.585})$.

Q: Why is Karatsuba FASTER than the schoolbook method despite more additions?
A: Because the crucial bottleneck is DEEP RECURSION, not surface additions: $3^{\log_2 n} = n^{1.585} < n^2$. For large $n$ (hundreds of digits), Karatsuba wins despite larger constants. Even faster: Toom-Cook ($n^{1.465}$) and Schönhage-Strassen ($n \log n \log \log n$) for astronomical integers (cryptography, pi computations). GMP/Python big-int libraries use these for large numbers.

## 8.6 Strassen's Matrix Multiplication

Q: Describe [Strassen's algorithm] for $n \times n$ matrix multiplication.
A: Naïve: $O(n^3)$. Divide each matrix into four $n/2 \times n/2$ blocks. Naïve recursion needs 8 block-multiplications: $T(n) = 8 T(n/2) + O(n^2) = O(n^3)$ — no gain. Strassen's trick: express product with 7 block-multiplications (plus many additions). Recurrence: $T(n) = 7 T(n/2) + O(n^2) = O(n^{\log_2 7}) \approx O(n^{2.807})$. Breaks the $n^3$ barrier, historic (1969).

Q: Why isn't Strassen's algorithm used in PRACTICE for small matrices?
A: Because it has LARGE CONSTANTS, worse numerical stability, and requires MORE MEMORY. For $n < $ ~500, naïve $O(n^3)$ is faster. Libraries (LAPACK, BLAS) use naïve for small, Strassen-like for medium, and increasingly sophisticated algorithms (Coppersmith-Winograd, $O(n^{2.37})$) for huge matrices. Current record: $O(n^{2.371})$ (Williams 2023). Lower bound: $\Omega(n^2)$; the true exponent $\omega$ is famously OPEN.

## 8.7 Closest Pair of Points

Q: Describe the [closest pair of points] divide-and-conquer algorithm.
A: Given $n$ points in the plane: (1) Sort by $x$-coordinate. (2) Split at median $x$. (3) Recursively find closest pair in each half: $d_L, d_R$. Let $\delta = \min(d_L, d_R)$. (4) COMBINE: look for closest pair straddling the divide, within a strip of width $2\delta$ around the median line. (5) Sort strip by $y$; check each point against only next 7 (proven sufficient). Time: $T(n) = 2T(n/2) + O(n) = O(n \log n)$. Beats naïve $O(n^2)$.

## 8.8 Fast Fourier Transform

Q: What does the [Fast Fourier Transform (FFT)] compute, and why is it $O(n \log n)$?
A: Transforms an $n$-point sequence $\{a_k\}$ into its DFT $\{A_j\}$ where $A_j = \sum_k a_k \omega^{jk}$ (with $\omega = e^{-2\pi i/n}$). Naïve: $O(n^2)$. FFT: divide-and-conquer, splitting odd and even indexed terms. Recurrence $T(n) = 2T(n/2) + O(n) = O(n \log n)$. Applications: POLYNOMIAL multiplication (multiply polynomials in $O(n \log n)$ via pointwise multiplication of DFTs), signal processing, image compression (JPEG uses DCT, a FFT variant).

Q: How does FFT enable FAST polynomial multiplication?
A: To multiply polynomials of degree $n$: (1) Evaluate each at $2n$ roots of unity via FFT — $O(n \log n)$. (2) Pointwise multiply the $2n$ values — $O(n)$. (3) Interpolate via inverse FFT — $O(n \log n)$. Total: $O(n \log n)$ vs naïve $O(n^2)$. Same idea fast-multiplies big integers (Schönhage-Strassen). Revolutionized signal processing and numerical computation in 1965 (Cooley-Tukey).

## 8.9 Quickselect (Finding the Median)

Q: Describe [quickselect] for finding the $k$-th smallest element.
A: Like quicksort, but after PARTITIONING by pivot: only RECURSE into the side containing the $k$-th element. (1) Partition around a pivot. (2) If pivot position $= k$, return pivot. (3) If $k <$ pivot position, recurse left. (4) Else recurse right with $k' = k - \text{pivot position}$. Expected time: $O(n)$ (geometric series). Worst-case (bad pivots): $O(n^2)$. Deterministic median-of-medians variant achieves $O(n)$ worst-case — a celebrated result.

Q: Describe the [median-of-medians] algorithm.
A: To select a good pivot deterministically: (1) Split input into groups of 5. (2) Find the median of each group (brute force, $O(n)$ total). (3) Recursively select the median-of-medians among these $n/5$ values. (4) Use this as pivot. Guaranteed pivot is "close" to the true median — pivot has $\geq 3n/10$ elements on each side. Gives $O(n)$ worst-case selection. Elegant and theoretically important, though randomized quickselect is faster in practice.

## 8.10 Polynomial Evaluation

Q: Describe [Horner's method] for polynomial evaluation.
A: Evaluate $p(x) = a_0 + a_1 x + \cdots + a_n x^n$ via nested form: $p(x) = a_0 + x(a_1 + x(a_2 + \cdots + x(a_{n-1} + x \cdot a_n) \cdots))$. Only $n$ multiplications and $n$ additions — $O(n)$, optimal. Not divide-and-conquer, but a clever reduction: avoids recomputing powers of $x$. Used in CPUs' fused multiply-add, compilers, numerical libraries.

## 8.11 When Divide and Conquer Fails

Q: Name a situation where divide-and-conquer is NOT the right approach.
A: When the COMBINE step is as expensive as solving the full problem. Example: computing the MAXIMUM of an array. Divide: $T(n) = 2 T(n/2) + O(1) = O(n)$. But a simple scan is also $O(n)$ with better constants and no recursion overhead. Divide-and-conquer wins when either (a) combining is sublinear (fewer operations than a naïve pass), or (b) subproblems can be solved faster than linear work individually. Without these, iteration wins.

## 8.12 A Worked Divide-and-Conquer Problem

P: Given an array of $n$ integers, find the MAXIMUM SUBARRAY SUM (contiguous) in $O(n \log n)$ via divide-and-conquer.
S:
**IDENTIFY**: "Find a contiguous subarray with maximum sum." Brute force: $O(n^2)$. Divide-and-conquer: $O(n \log n)$. (Kadane's linear-time algorithm is also known; here we illustrate the paradigm.)

**PLAN**: Max subarray is either entirely in left half, entirely in right half, or CROSSES the middle. Divide, conquer each half recursively, combine by computing max-crossing-subarray.

**EXECUTE**:
```
MAX-SUBARRAY(A, l, r):
    if l == r: return A[l]  # single element
    m = (l + r) / 2
    left_max = MAX-SUBARRAY(A, l, m)
    right_max = MAX-SUBARRAY(A, m+1, r)
    cross_max = MAX-CROSSING(A, l, m, r)
    return max(left_max, right_max, cross_max)

MAX-CROSSING(A, l, m, r):
    # left portion: max suffix ending at m
    left_sum = -infinity; s = 0
    for i = m downto l: s += A[i]; left_sum = max(left_sum, s)
    # right portion: max prefix starting at m+1
    right_sum = -infinity; s = 0
    for i = m+1 to r: s += A[i]; right_sum = max(right_sum, s)
    return left_sum + right_sum
```

**EVALUATE**: MAX-CROSSING is $O(n)$. Recurrence: $T(n) = 2 T(n/2) + O(n) = O(n \log n)$ by master theorem. Example: $A = [-2, 1, -3, 4, -1, 2, 1, -5, 4]$. Max subarray = $[4, -1, 2, 1]$, sum 6. Algorithm recursively finds this: left half's answer, right half's answer, or crossing — the crossing case finds it here (starting at index 3 in the left half, extending into right). This algorithm was famously beaten by Kadane's $O(n)$ one-pass DP — but divide-and-conquer version generalizes to multi-dimensional cases and parallelizes well.
