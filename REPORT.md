# Question 7 – Hash Table for Song IDs (Group 7)

**Keys:** 105, 210, 315, 420, 525, 630, 735, 840
**Hash function (Division Method):** h(k) = k mod m
**Collision resolution:** Linear probing, index = (h(k) + i) mod m

Two table sizes were tested: **m = 10** (a natural but poor choice) and **m = 11** (prime).

---
## (a) Insertion and collisions (m = 10)

| Insert | h(k) = k mod 10 | Collision? | Placed at |
|-------:|:---------------:|:----------:|:---------:|
| 105 | 5 | No  | 5 |
| 210 | 0 | No  | 0 |
| 315 | 5 | Yes (1 probe extra) | 6 |
| 420 | 0 | Yes (1) | 1 |
| 525 | 5 | Yes (2) | 7 |
| 630 | 0 | Yes (2) | 2 |
| 735 | 5 | Yes (3) | 8 |
| 840 | 0 | Yes (3) | 3 |

**Table after each insertion** (only occupied slots shown; empty slots omitted):

| After inserting | Table contents (slot: key) |
|---|---|
| 105 | 5:105 |
| 210 | 0:210, 5:105 |
| 315 | 0:210, 5:105, 6:315 |
| 420 | 0:210, 1:420, 5:105, 6:315 |
| 525 | 0:210, 1:420, 5:105, 6:315, 7:525 |
| 630 | 0:210, 1:420, 2:630, 5:105, 6:315, 7:525 |
| 735 | 0:210, 1:420, 2:630, 5:105, 6:315, 7:525, 8:735 |
| 840 | 0:210, 1:420, 2:630, 3:840, 5:105, 6:315, 7:525, 8:735 |

**Final table (m = 10):** slots 4 and 9 are empty.
**Collisions:** 6 of 8 insertions collided; 12 extra probes in total.

**Why so many?** Every ID is a multiple of 105, so `k mod 10` is only ever 0 or 5. All keys pile into two home slots (primary clustering).

**With m = 11 (prime):** hash values are 6,1,7,2,8,3,9,4, so there are **0 collisions**.

---
## (b) Search: hashing vs linear search

Searched IDs: 105, 420, 840 (present) and 999 (absent). Cost = number of comparisons.

| Key | Hashing m=10 | Hashing m=11 | Linear search |
|----:|:-----------:|:------------:|:-------------:|
| 105 | 1 | 1 | 1 |
| 420 | 2 | 1 | 4 |
| 840 | 4 | 1 | 8 |
| 999 (absent) | 1 | 2 | 8 |
| **Average** | **2.00** | **1.25** | **5.25** |

---
## (c) Analysis

**Load factor** α = n / m
- m = 10: α = 8/10 = **0.80**
- m = 11: α = 8/11 ≈ **0.73**

**Effect of collisions.** With m = 10, keys 420, 630 and 840 (and 315, 525, 735) are displaced from their home slot, so a search must probe several slots before finding them. 840 needs 4 comparisons, which is worse than a well-spread table. With m = 11, no key is displaced and every successful search takes 1 comparison.

**Theoretical vs observed**

| Method | Theoretical | Observed (n = 8) |
|---|---|---|
| Linear search | O(n) worst/average | up to 8 comparisons, avg 5.25 |
| Hashing (ideal) | O(1) average | avg 1.25 (m = 11) |
| Hashing (poor hash / clustering) | degrades toward O(n) | avg 2.00 (m = 10) |

For linear probing, the expected probes at load factor α are about
½(1 + 1/(1−α)) for a successful search and ½(1 + 1/(1−α)²) for an unsuccessful one.
At α = 0.8 that gives about 3 and 13 probes, so performance worsens quickly as the table fills. Our observed values are lower because only a few keys were tested, but they show the same trend: the clustered m = 10 table costs more than the m = 11 table.

**Conclusion – is hashing suitable?**
Yes. Song lookup by ID is an exact-match operation, which hashing does in O(1) average time against O(n) for linear search, and the gap widens as the catalogue grows. It works well only if (1) the table size is a **prime** not sharing factors with the key pattern (m = 11 rather than 10), and (2) the load factor is kept below about 0.5–0.7, resizing/rehashing when it is exceeded. Hashing is not suitable if ordered or range queries are needed (for example, songs between two IDs); a balanced tree would suit that better.
