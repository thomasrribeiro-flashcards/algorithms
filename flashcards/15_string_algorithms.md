+++
order = 15
subject = "computer-science"
tags = ["cs", "algorithms", "strings", "kmp", "rabin-karp", "suffix-tree", "suffix-array", "aho-corasick"]
+++

# Algorithms — String Algorithms

## 15.1 String Matching: The Problem

Q: What is the [exact string matching] problem?
A: Given a TEXT $T$ of length $n$ and a PATTERN $P$ of length $m$: find all occurrences of $P$ in $T$. Naive algorithm: for each position $i$ in $T$, check if $P$ matches $T[i..i+m-1]$ — $O(nm)$ worst-case. Efficient algorithms achieve $O(n + m)$: KMP, Rabin-Karp (expected), Boyer-Moore (sublinear in practice), Z-algorithm. Ubiquitous: grep, editors, compilers, bioinformatics, DNA searching.

## 15.2 Knuth-Morris-Pratt (KMP)

Q: Describe the [KMP algorithm].
A: Preprocesses pattern into a FAILURE FUNCTION (or PREFIX FUNCTION): $\pi[i]$ = length of the longest PROPER prefix of $P[0..i]$ that is also a suffix. When a mismatch occurs at position $i$ in $P$: shift pattern by $i - \pi[i-1]$, continuing from $P[\pi[i-1]]$ rather than restarting. Avoids re-examining already-matched text characters. Time: $O(n + m)$. Space: $O(m)$.

Q: Why does KMP achieve $O(n + m)$ LINEAR time?
A: Because each character of $T$ is examined AT MOST twice (once by the match pointer, once by a "failure" step). The failure function preprocessing is $O(m)$. Analyzed via amortization: each successful match advances the text pointer; each failure regresses pattern pointer but can't regress text pointer. Total operations bounded by $2n$. Classical example of clever preprocessing turning $O(nm)$ into $O(n + m)$.

## 15.3 Rabin-Karp

Q: Describe the [Rabin-Karp] algorithm.
A: Uses HASHING. Compute hash of pattern $P$ and hash of each length-$m$ window of $T$. Compare hashes; on match, verify character-by-character. ROLLING HASH: update window hash in $O(1)$ by subtracting outgoing character's contribution and adding incoming. Expected $O(n + m)$ with good hash; worst-case $O(nm)$ on hash collisions. Useful for MULTI-PATTERN matching: one query per window for a set of patterns hashed.

Q: What is a [rolling hash], and why is it crucial for Rabin-Karp?
A: A hash function $h$ on strings such that $h(T[i+1..i+m])$ can be computed from $h(T[i..i+m-1])$ in $O(1)$. Typical choice: $h(s_0 s_1 \ldots s_{m-1}) = \sum s_j b^{m-1-j} \mod p$ for base $b$ and prime $p$. Rolling: $h_{new} = (b \cdot (h_{old} - s_0 b^{m-1}) + s_m) \mod p$. Enables $O(n)$ total hash computations. Used in: plagiarism detection (Rabin fingerprints), content-defined chunking, distributed systems.

## 15.4 Boyer-Moore

Q: Describe [Boyer-Moore] at a high level.
A: Match pattern against text RIGHT-TO-LEFT. On mismatch, shift pattern using two heuristics: (1) [Bad character]: shift pattern so the mismatched text character aligns with its rightmost occurrence in pattern (or past it). (2) [Good suffix]: shift so the matched suffix aligns with another occurrence in pattern. Can SKIP entire blocks of text — sublinear in practice ($O(n/m)$ on typical inputs). Worst case: $O(nm)$; with Galil's fix, $O(n)$. `grep`'s default algorithm.

## 15.5 Suffix Trees

Q: What is a [suffix tree] of a string $S$?
A: A COMPRESSED TRIE containing all suffixes of $S \# $ (with an end-marker). Each edge labeled with a substring; paths from root to leaves spell suffixes. Size $O(n)$ (despite $n$ suffixes of total length $O(n^2)$). Can be built in $O(n)$ (Ukkonen, McCreight). Supports MANY queries in $O(m)$ or $O(n)$: substring search, longest repeated substring, longest common substring, longest palindrome. Exchange $O(n)$ space for amazing query speed.

Q: Name FIVE problems efficiently solved by suffix trees.
A: (1) [Substring search]: is $P$ in $S$? — $O(|P|)$. (2) [Count occurrences]: number of times $P$ appears. (3) [Longest repeated substring]: deepest internal node. (4) [Longest common substring] of multiple strings: build generalized suffix tree. (5) [Maximal palindromes]. Also: tandem repeats, approximate matching, data compression. Suffix trees turn string problems into tree-traversal problems.

## 15.6 Suffix Arrays

Q: What is a [suffix array] of a string $S$?
A: SORTED array of starting indices of all suffixes of $S$. $SA[i]$ = starting index of the $i$-th lexicographically smallest suffix. Equivalent power to suffix trees but MUCH SIMPLER (array of integers vs tree of nodes/edges). Construction: $O(n \log n)$ naive; $O(n)$ via DC3 / SA-IS algorithms. Storage: $O(n)$ integers. With auxiliary LCP (longest common prefix) array, supports most suffix-tree queries in $O(m \log n)$.

Q: Compare SUFFIX TREE vs SUFFIX ARRAY.
A: Suffix tree: larger constant factor ($\sim 20n$ bytes), $O(m)$ queries, complex implementation. Suffix array: compact ($4n$–$8n$ bytes), $O(m \log n)$ queries, much simpler. In PRACTICE, suffix arrays are preferred for most string-processing tasks (indexing, compression via Burrows-Wheeler, bioinformatics). Enhanced suffix arrays (with LCP + extra tables) recover suffix-tree power.

## 15.7 Z-Algorithm

Q: What does the [Z-algorithm] compute, and how is it used?
A: Given string $S$, computes $z[i]$ = length of the longest substring starting at $S[i]$ that MATCHES a prefix of $S$. $O(n)$ time. Applications: (1) String matching — concatenate $P + \# + T$ and compute $z$; positions with $z[i] \geq |P|$ are matches. (2) Pattern preprocessing. (3) Many problems that ask "substring equals prefix." Often simpler to implement than KMP's failure function while achieving the same $O(n + m)$ matching.

## 15.8 Aho-Corasick

Q: What does [Aho-Corasick] solve?
A: MULTI-PATTERN string matching: given $k$ patterns $P_1, \ldots, P_k$ and text $T$: find all occurrences of ALL patterns in $T$. Build a TRIE of patterns augmented with FAILURE LINKS (KMP-style, but across tree). Single pass through $T$ finds all matches in $O(n + \sum |P_i| + |\text{matches}|)$. Used in: intrusion detection (Snort), spam filtering, genomics (multiple motif search), lexical analysis. Generalization of KMP.

## 15.9 Burrows-Wheeler Transform

Q: What is the [Burrows-Wheeler Transform (BWT)], and why is it used?
A: Reversible rearrangement of a string: sort all cyclic rotations; output the LAST COLUMN. Produces a string with LONG RUNS of identical characters (because similar-context characters cluster) — highly compressible. Combined with MTF + RLE + Huffman: bzip2 compression. Also enables EFFICIENT SEARCHING in compressed text (FM-index). Revolutionary for: genome indexing (BWA, Bowtie), compressed file systems.

## 15.10 Tries and Text Indexing

Q: Compare tries, suffix trees, and suffix arrays for DIFFERENT use cases.
A: [Trie]: stores a SET of strings; prefix queries on dictionary. (Words like "Autocomplete.") Simple. [Suffix tree/array]: stores all suffixes of ONE string; substring queries efficient. More powerful but complex. Trie of patterns + failure links → Aho-Corasick for multi-pattern matching. For search engines: [inverted index] (document → list of positions) — essentially a trie/hashmap over words + posting lists.

## 15.11 Approximate Matching and Edit Distance

Q: How do you find matches with AT MOST $k$ errors?
A: (1) [DP-based]: extend edit-distance DP to find $P$'s best alignment at each position of $T$. $O(nm)$ classical; $O(nk)$ with bit-parallelism (Bitap). (2) [q-gram filtering]: exact match of $q$-grams reduces candidates; verify with DP. (3) [Suffix automaton + DP]. Used in bioinformatics (BLAST for sequence similarity), fuzzy search in code editors, spell checkers, OCR post-processing.

## 15.12 A Worked KMP Example

P: Compute the KMP failure function for pattern $P = \text{"ABABCABAB"}$, then use it to find occurrences in $T = \text{"ABABDABACDABABCABAB"}$.
S:
**IDENTIFY**: Failure function $\pi$ for $P$, then matching.

**PLAN**: Build $\pi$ by the standard two-pointer method; then scan $T$.

**EXECUTE**:
Compute $\pi$ for $P = A B A B C A B A B$ (0-indexed).
- $\pi[0] = 0$ (no proper prefix).
- $\pi[1] = 0$: "AB" has no proper prefix = suffix.
- $\pi[2] = 1$: "ABA" has prefix "A" = suffix.
- $\pi[3] = 2$: "ABAB" — "AB".
- $\pi[4] = 0$: "ABABC" — no match (C breaks it).
- $\pi[5] = 1$: "ABABCA" — "A".
- $\pi[6] = 2$: "ABABCAB" — "AB".
- $\pi[7] = 3$: "ABABCABA" — "ABA".
- $\pi[8] = 4$: "ABABCABAB" — "ABAB".

$\pi = [0, 0, 1, 2, 0, 1, 2, 3, 4]$.

Scan $T = A B A B D A B A C D A B A B C A B A B$ (length 19):
- Match $T[0..3] = $ ABAB against $P$: positions 0-3 match. At $P[4]$ (C), $T[4]$ = D: mismatch. Shift: $\pi[3] = 2$, resume at $P[2]$. Text pointer stays at 4.
- $P[2] = A$, $T[4] = D$: mismatch. $\pi[1] = 0$, resume at $P[0]$. $P[0] = A$, $T[4] = D$: mismatch. Advance $T$ to 5.
- $T[5] = A$ matches $P[0]$. Continue: ABABA... $T[5..8] = ABAC$. At $P[3] = B, T[8] = C$: mismatch. $\pi[2] = 1$, resume at $P[1]$.
- Continue — at $T[10..18] = ABABCABAB$, matches $P$ fully. Occurrence at position 10.

**EVALUATE**: Single match at position 10. KMP made $O(n + m) = O(19 + 9) = O(28)$ character comparisons, vs naive $O(nm) = O(171)$ in worst case. The failure function $\pi$ encodes how far to rewind — allowing the algorithm to NEVER backtrack in $T$, only shift in $P$. This linear-time guarantee is the key: grep's default matching is all variants of this idea.
