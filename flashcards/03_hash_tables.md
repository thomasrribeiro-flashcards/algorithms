+++
order = 3
subject = "computer-science"
tags = ["cs", "algorithms", "data-structures", "hash-table", "hashing", "collision", "chaining", "open-addressing"]
+++

# Algorithms — Hash Tables

## 3.1 The Dictionary Problem

Q: What is the [dictionary] (or map) ADT?
A: A key-value store supporting: INSERT(key, value), LOOKUP(key) → value, DELETE(key). Keys are unique. Hash tables implement the dictionary with $O(1)$ AVERAGE time operations — a foundational data structure for databases, compilers, caches, sets, memoization. The challenge: finding and updating values quickly without sorting the keys.

## 3.2 Hash Functions

C: A [hash function] $h : \text{Keys} \to \{0, 1, \ldots, m - 1\}$ maps keys to indices in a table of size $m$.

Q: What makes a GOOD hash function?
A: (1) [Uniformity]: distributes keys evenly across $\{0, \ldots, m-1\}$. (2) [Determinism]: same key → same hash. (3) [Speed]: $O(\text{key size})$ to compute. (4) [Avalanche]: small input change → large hash change, reducing clustering. Cryptographic hashes (SHA-256) are strong but slow; fast hashes (MurmurHash, FNV, SipHash) balance quality and speed for in-memory use. Production: Python uses SipHash (DoS-resistant); Java uses custom polynomial for strings.

## 3.3 Collisions

Q: Why are COLLISIONS unavoidable in hash tables?
A: Because the PIGEONHOLE PRINCIPLE: if the key space is larger than $m$ (almost always), two distinct keys must map to the same index. For $m$ slots and random keys, the [birthday paradox] says collisions become likely after only $\sim \sqrt{m}$ insertions. Hash tables must HANDLE collisions — the question is HOW, not IF.

## 3.4 Collision Resolution: Chaining

Q: Describe [chaining] collision resolution.
A: Each table slot stores a LINKED LIST (or small dynamic array) of all key-value pairs hashing there. Insert: prepend to the list. Lookup: hash to slot, linear search the list. Delete: hash, find, remove. Under uniform hashing with load factor $\alpha = n/m$ (entries per slot), average chain length is $\alpha$; expected lookup time $O(1 + \alpha)$. Keep $\alpha \leq 1$ by resizing when table fills.

Q: What's the WORST-CASE behavior of chaining?
A: $O(n)$ per operation if ALL keys hash to the same slot — adversarial input against a known hash. Defense: RANDOMIZED hash (SipHash, keyed from random initialization), cryptographic hashes, or UNIVERSAL HASHING (random choice from a hash family). Production hash tables use random keys to prevent hash-flooding attacks that exploit deterministic hashes.

## 3.5 Open Addressing

Q: Describe [open addressing] collision resolution.
A: Store all entries IN THE TABLE itself (no linked lists). On collision, PROBE a sequence of slots until finding an empty one. Lookup follows the same probe sequence until finding the key or an empty slot. Variants: LINEAR PROBING ($h + 1, h + 2, \ldots$), QUADRATIC PROBING ($h + 1^2, h + 2^2, \ldots$), DOUBLE HASHING ($h + i \cdot h_2(k)$). Deletion is tricky — must use "tombstones" or re-insert downstream.

Q: Trade-offs between CHAINING and OPEN ADDRESSING?
A: Chaining: simpler, handles high load factors ($\alpha > 1$), easier deletion, but extra memory for pointers. Open addressing: better cache behavior (contiguous), less memory per entry, but degrades sharply as $\alpha \to 1$ (clustering under linear probing). Modern production tables often use HYBRIDS (e.g., Robin Hood hashing, Swiss tables, HOPSCOTCH). Benchmarks favor open addressing at low $\alpha$ (<0.7); chaining at high.

## 3.6 Load Factor and Resizing

C: The [load factor] of a hash table is $\alpha = n/m$ (entries / table slots).

Q: When should a hash table RESIZE?
A: When load factor exceeds a threshold (typically $\alpha > 0.75$ for chaining, $0.5$–$0.9$ for open addressing depending on variant). Resize: allocate new table (usually $2\times$ size), REHASH every entry (hash value depends on table size, since $h(k) \mod m$ changes). Amortized $O(1)$ per insert. Shrink optional: halve the table when $\alpha < 0.25$ to reclaim memory.

Q: Why can't we just INCREMENT the table size by 1 on overflow?
A: Because each resize costs $O(n)$ — and doing it frequently gives $\sum_{i=1}^n O(i) = O(n^2)$ total work. Doubling (geometric growth) gives $O(n)$ total via amortization: resize costs $1 + 2 + 4 + \cdots + n/2 + n < 2n$. Same principle as dynamic array growth. Halving on shrink requires care: avoid "thrashing" when size oscillates — use different thresholds for growth (0.75) and shrink (0.25).

## 3.7 Universal Hashing

Q: What is [universal hashing], and why does it matter?
A: A FAMILY of hash functions $\mathcal{H}$ such that for any two distinct keys $x \neq y$: $P_{h \sim \mathcal{H}}[h(x) = h(y)] \leq 1/m$. RANDOMLY select $h$ from $\mathcal{H}$ at table creation; no adversarial input can force worst-case behavior against a RANDOM $h$. Provides THEORETICAL $O(1)$ expected time even for worst-case inputs. Simple example: $h_{a,b}(k) = ((a k + b) \mod p) \mod m$ for random $a, b$ and prime $p > k$.

## 3.8 Perfect Hashing

Q: What is [perfect hashing]?
A: A hash function that is INJECTIVE on a FIXED set of keys — zero collisions. For $n$ known keys, one can construct a hash function with NO COLLISIONS in $O(n)$ expected time (Fredman-Komlós-Szemerédi 1984). Used in STATIC dictionaries: compilers, interpreters, read-only databases where the key set is fixed in advance. [Minimal perfect hashing]: maps $n$ keys to $\{0, \ldots, n-1\}$ bijectively, using $\approx n$ bits per key.

## 3.9 Bloom Filters

Q: What is a [Bloom filter], and when is it useful?
A: A SPACE-EFFICIENT probabilistic data structure answering "does $x$ belong to set $S$?". Uses $k$ independent hash functions into a bit array. Insert: set all $k$ hashed bits. Query: check all $k$ bits; if any is 0, DEFINITELY not in $S$; if all 1, PROBABLY in $S$ (false positive possible, never false negative). $\sim 10$ bits per element for 1% false positive rate. Uses: caches, network routers, database query optimizers, duplicate detection in streams.

## 3.10 Cuckoo Hashing

Q: Describe [cuckoo hashing].
A: Use TWO hash functions $h_1, h_2$ and two tables. Each key lives in table 1 at $h_1(k)$ OR table 2 at $h_2(k)$. Lookup: check both slots — $O(1)$ worst-case. Insert: place at $h_1(k)$; if occupied, EVICT incumbent and place it at its alternate slot (using the other hash function); repeat, cascading. If a cycle forms, REHASH. Worst-case lookup is $O(1)$; amortized insert is $O(1)$ expected. Used in practice when worst-case guarantees matter.

## 3.11 Hash Sets and Sets via Hashing

Q: How do SETS (collections without values) use hash tables?
A: A hash set is a hash table with keys only — no associated values. Supports INSERT, LOOKUP, DELETE, membership test in $O(1)$ expected. Common implementation of `Set` in Python (frozenset/set), Java (HashSet), C++ (`std::unordered_set`). Operations UNION, INTERSECTION, DIFFERENCE are $O(\min(|A|, |B|))$ by iterating the smaller and querying the larger.

## 3.12 A Worked Hash Table Analysis

P: Analyze expected lookup time in a hash table with chaining, load factor $\alpha = n/m$, assuming uniform hashing.
S:
**IDENTIFY**: Expected lookup time = 1 (hash computation) + expected length of chain searched.

**PLAN**: For an UNSUCCESSFUL search: the key is missing, so we scan the full chain at $h(k)$. Expected chain length = $\alpha$. For SUCCESSFUL search: average over positions of the key in its chain.

**EXECUTE**:
Unsuccessful: $\mathbb{E}[\text{chain length at slot } h(k)] = n/m = \alpha$. Time: $O(1 + \alpha)$.

Successful: let key $k_i$ be the $i$-th inserted. Under uniform hashing, chain at insertion time contained $\mathbb{E}[(i-1)/m]$ earlier keys. When we look up $k_i$ later, all $n - i$ keys inserted AFTER $k_i$ may also be in its chain, contributing $(n - i)/m$ on average. Position of $k_i$ in its chain (assuming prepend-insert): expected $1 + (n - i)/m$ others to scan past. Time = $1 + (n - i)/m$. Average over $i$:
$\frac{1}{n}\sum_{i=1}^n \left[1 + \frac{n - i}{m}\right] = 1 + \frac{1}{m} \cdot \frac{n - 1}{2} \approx 1 + \frac{\alpha}{2}$.

**EVALUATE**: Both successful and unsuccessful searches are $O(1 + \alpha)$ expected. For $\alpha < 1$ (e.g., $\alpha = 0.75$), this is effectively constant. As $\alpha$ grows beyond 1, search time grows linearly with $\alpha$ — motivating resize on $\alpha > 0.75$. Real production tables (Google's Swiss Table, abseil) use OPEN ADDRESSING with metadata bytes to achieve $O(1)$ amortized AND better cache behavior — but the $O(1 + \alpha)$ analysis is the classical foundation.
