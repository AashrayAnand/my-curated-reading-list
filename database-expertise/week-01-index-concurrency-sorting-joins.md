# Week 1: Index Concurrency, Sorting, Aggregation, and Joins

**Dates:** August 10-16, 2026

**Budget:** About 7-7.5 hours total, 50-75 minutes per day

**Theme:** Protecting shared index structures and choosing physical algorithms

This week repairs the prerequisites immediately before query execution. The target is not to
memorize every algorithm. It is to understand what state each algorithm owns, what resource limits
its performance, and what concurrency or data-shape assumptions make it correct. It also starts
the six-week [query-engine project](query-engine-project.md) with a deliberately small setup
milestone.

Write the week's annotations in [notes/week-01.md](notes/week-01.md). Checkboxes in this file track
consumption; the notes file holds recall, calculations, experiments, and the retrospective.

## Definition of done

- [ ] Watch lectures 10-12 and write five closed-book bullets after each.
- [ ] Complete the scoped textbook, paper, and engineering readings for all three topics.
- [ ] Draw latch acquisition and release for a B+ tree lookup, a safe insert, and an insert that
  splits a leaf.
- [ ] Calculate one external-sort cost, one block nested-loop join cost, and one Grace hash join
  cost.
- [ ] Run the DuckDB practicum and explain why it selected sort, hash aggregation, and hash join
  operators.
- [ ] Create the query-engine lab, pin the KQuery reference revision, and commit a passing smoke
  test plus an architecture map.
- [ ] Give a five-minute teach-back: "How do latches, memory, ordering, and input cardinality shape
  physical database algorithms?"

## Topic 1 -- Latching and index concurrency

### Lecture

- [ ] **CMU 15-445 #10: Index Concurrency Control**
  - [Public Fall 2025 video](https://www.youtube.com/watch?v=YgOvfXl6pss&list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5&index=10)
  - [Spring 2026 slides](https://15445.courses.cs.cmu.edu/spring2026/slides/10-indexconcurrency.pdf)
  - [Spring 2026 notes](https://15445.courses.cs.cmu.edu/spring2026/notes/10-indexconcurrency.pdf)
  - **Category:** Foundational, Practical
  - **Why:** Separates logical transaction locks from short-lived physical latches, then develops
    latch crabbing, safe-node tests, optimistic traversal, and root contention.
  - **Time:** About 50 minutes at 1.5x.

### Textbook

- [ ] **Database System Concepts, 7th ed. -- Section 18.10.2**
  - [Book site](https://www.db-book.com/)
  - **Category:** Foundational
  - **Why:** Provides the ordered description of tree locking and crabbing that the lecture assumes.
  - **Time:** 10 minutes. Use the CMU notes as the open-access fallback.

### Paper

- [ ] **Efficient Locking for Concurrent Operations on B-Trees** -- Lehman and Yao
  - [Open PDF](https://www.cs.cmu.edu/~natassa/courses/15-721/papers/p650-lehman.pdf)
  - **Category:** Historical Context, Pattern Recognition
  - **Why:** Introduces the B-link tree's high keys and right links, showing how a small structural
    change permits concurrent searches and splits without holding a latch on the full path.
  - **Scope:** Read the abstract, motivation, search/insert algorithm, split example, and
    conclusion. Skim the proof details on the first pass.
  - **Time:** 15 minutes.

### Engineering article

- [ ] **To B or not to B: B-Trees with Optimistic Lock Coupling** -- CedarDB
  - [Article](https://cedardb.com/blog/optimistic_btrees/)
  - **Category:** Practical, Mental Model
  - **Why:** Connects latch coupling to cache coherence, root metadata contention, sequence locks,
    optimistic validation, and a modern many-core implementation.
  - **Time:** 15 minutes.

### Optional implementation reference

- [ ] **Optional: PostgreSQL nbtree README -- Lehman and Yao section**
  - [Source documentation](https://github.com/postgres/postgres/blob/master/src/backend/access/nbtree/README)
  - **Category:** Practical, Implementation Reference
  - **Why:** Shows how a mature engine adapts high keys, right links, page splits, and duplicate-key
    handling.
  - **Scope:** Search for "Lehman and Yao" and read through the page-split discussion.
  - **Time:** 10 minutes.

### Retrieval questions

1. Why is a latch not the same thing as a transaction lock?
2. When is a child "safe" for lookup, insertion, and deletion?
3. Why must latch crabbing acquire the child before releasing the parent?
4. How do a high key and right link let a search recover from a concurrent split?
5. Why can even shared root latches cause cache-coherence contention?

## Topic 2 -- Sorting and aggregation

### Lecture

- [ ] **CMU 15-445 #11: Sorting & Aggregations Algorithms**
  - [Public Fall 2025 video](https://www.youtube.com/watch?v=LzyKTpeIgts&list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5&index=11)
  - [Spring 2026 slides](https://15445.courses.cs.cmu.edu/spring2026/slides/11-sorting.pdf)
  - [Spring 2026 notes](https://15445.courses.cs.cmu.edu/spring2026/notes/11-queryI.pdf)
  - **Category:** Foundational, Mental Model
  - **Why:** Builds external merge sort from the buffer budget and compares sorting and hashing for
    duplicate elimination and grouped aggregation.
  - **Time:** About 50 minutes at 1.5x.

### Textbook

- [ ] **Database System Concepts, 7th ed. -- Sections 15.4-15.5**
  - [Book site](https://www.db-book.com/)
  - **Category:** Foundational
  - **Why:** Makes the I/O model explicit: run generation, merge fan-in, number of passes, and
    evaluation of sorting and aggregation.
  - **Time:** 15 minutes. Use the CMU notes as the open-access fallback.

### Paper

- [ ] **Implementing Sorting in Database Systems** -- Goetz Graefe
  - [University-hosted PDF](http://wwwlgis.informatik.uni-kl.de/archiv/wwwdvs.informatik.uni-kl.de/courses/DBSREAL/SS2005/Vorlesungsunterlagen/Implementing%5FSorting.pdf)
  - **Category:** Pattern Recognition, Implementation Reference
  - **Why:** Surveys the engineering choices hidden behind "external merge sort": run generation,
    merge strategies, memory allocation, parallelism, and interactions with other operators.
  - **Scope:** Read the introduction, external-sort overview, memory/I/O optimizations, and summary.
  - **Time:** 15 minutes.

### Engineering articles

- [ ] **Fastest Table Sort in the West -- Redesigning DuckDB's Sort**
  - [Article](https://duckdb.org/2021/08/27/external-sorting.html)
  - **Category:** Practical, Cutting Edge
  - **Why:** Turns external sorting into real implementation decisions: binary-key encoding, radix
    sort, payload movement, parallel run generation, and spilling.
  - **Time:** 15 minutes.

- [ ] **Parallel Grouped Aggregation in DuckDB**
  - [Article](https://duckdb.org/2022/03/07/aggregate-hashtable.html)
  - **Category:** Practical, Pattern Recognition
  - **Why:** Explains why grouped aggregation often uses hash tables, how collisions and resizing
    affect locality, and how local tables are merged in parallel.
  - **Time:** 10 minutes.

### Retrieval questions

1. For a relation of `N` pages and `B` buffer pages, how many initial runs are created?
2. What determines merge fan-in and the number of merge passes?
3. When can a final sort or merge pass be pipelined into the next operator?
4. When is sort-based aggregation preferable to hash aggregation?
5. What happens when the hash table or sort state exceeds its memory budget?

## Topic 3 -- Join algorithms

### Lecture

- [ ] **CMU 15-445 #12: Joins Algorithms**
  - [Public Fall 2025 video](https://www.youtube.com/watch?v=YIdIaPopfpk&list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5&index=12)
  - [Spring 2026 slides](https://15445.courses.cs.cmu.edu/spring2026/slides/12-joins.pdf)
  - [Spring 2026 notes](https://15445.courses.cs.cmu.edu/spring2026/notes/12-joins.pdf)
  - **Category:** Foundational, Practical
  - **Why:** Compares tuple and block nested loops, index nested loops, sort-merge join, in-memory
    hash join, and partitioned/Grace hash join using explicit cost models.
  - **Time:** About 50 minutes at 1.5x.

### Textbook

- [ ] **Database System Concepts, 7th ed. -- Sections 15.4-15.6**
  - [Book site](https://www.db-book.com/)
  - **Category:** Foundational
  - **Why:** Supplies the assumptions and page-I/O equations needed to compare physical joins rather
    than ranking them by intuition.
  - **Scope:** Read the join subsections; do not reread the sorting material from Topic 2.
  - **Time:** 15 minutes.

### Paper

- [ ] **An Experimental Comparison of Thirteen Relational Equi-Joins in Main Memory** -- Schuh,
  Chen, and Dittrich
  - [Open PDF](https://www.cs.cmu.edu/~15721-f25/papers/Eval_13_Join_Algos.pdf)
  - **Category:** Pattern Recognition, Practical
  - **Why:** Demonstrates that "hash join is fastest" is not a universal rule. Hardware, radix
    partitioning, tuple width, skew, and implementation quality all change the result.
  - **Scope:** Read the algorithm taxonomy, experimental setup, result summary, and conclusion.
  - **Time:** 20 minutes.

### Engineering article

- [ ] **40x Faster Hash Joiner with Vectorized Execution** -- Cockroach Labs
  - [Article](https://www.cockroachlabs.com/blog/vectorized-hash-joiner/)
  - **Category:** Practical, Pattern Recognition
  - **Why:** Walks through build/probe state, duplicate matches, selection vectors, and why data
    layout can dominate the textbook algorithm.
  - **Time:** 15 minutes.

### Retrieval questions

1. Why should block nested-loop join put the smaller relation on the outer side?
2. When does index nested-loop join beat a hash join?
3. What work can sort-merge join reuse if an input is already ordered?
4. Why does a hash join build on the smaller input?
5. What assumptions must hold for Grace hash join partitions to fit in memory, and how does skew
   break them?

## Practicum -- Trace, calculate, inspect

Record all predictions and results in [notes/week-01.md](notes/week-01.md).

### Part A -- Trace B+ tree latches

Draw a three-level B+ tree and trace:

1. a point lookup using read latches;
2. an insertion into a leaf with free space;
3. an insertion that splits the leaf and propagates a separator upward.

For every step, write which pages are latched, the latch mode, the safe-node test, and the earliest
release point.

### Part B -- Calculate the I/O costs

Use the lecture formulas:

1. **External sort:** `N = 10,000` pages and `B = 101` buffer pages. Calculate initial runs, merge
   fan-in, merge passes, and total page I/O.
2. **Block nested-loop join:** `R = 1,000` pages, `S = 5,000` pages, and `B = 102` buffers. Choose
   the outer relation and calculate the page-I/O cost.
3. **Grace hash join:** Using the same relations, calculate the idealized `3(R + S)` cost and list
   the assumptions required for that estimate to hold.

### Part C -- Inspect physical operators

Use a local [DuckDB](https://duckdb.org/install/) installation. The
[TPC-H extension](https://duckdb.org/docs/current/core_extensions/tpch.html) and
[EXPLAIN guide](https://duckdb.org/docs/current/guides/meta/explain.html) are references.

```sql
INSTALL tpch;
LOAD tpch;
CALL dbgen(sf = 0.1);

EXPLAIN ANALYZE
SELECT l_orderkey, l_extendedprice
FROM lineitem
ORDER BY l_extendedprice DESC;

EXPLAIN ANALYZE
SELECT l_returnflag, l_linestatus, sum(l_quantity)
FROM lineitem
GROUP BY l_returnflag, l_linestatus;

EXPLAIN ANALYZE
SELECT c_mktsegment, count(*) AS order_count
FROM customer
JOIN orders ON c_custkey = o_custkey
GROUP BY c_mktsegment
ORDER BY c_mktsegment;
```

Identify `ORDER_BY`, `HASH_GROUP_BY` or `PERFECT_HASH_GROUP_BY`, and `HASH_JOIN`. Before each
command, predict the operator and the state it must retain. Afterward, explain why that choice fits
the data and where a different memory budget, ordering, index, or skew could change the plan.

### Durable output

Complete the final synthesis in [notes/week-01.md](notes/week-01.md):

> For each mechanism -- latch crabbing, external sort, hash aggregation, and hash join -- what
> invariant makes it correct, what resource limits it, and what workload makes it a poor choice?

## Query-engine project -- Milestone 0

This is setup, not a second implementation assignment. Keep it to 30-45 focused minutes.

- [ ] Read [The KQuery Project](https://howqueryengineswork.com/00-source-code.html) and
  [What Is a Query Engine?](https://howqueryengineswork.com/01-what-is-a-query-engine.html).
- [ ] Keep the
  [KQuery companion repository](https://github.com/andygrove/how-query-engines-work) as a
  read-only reference and record the `main` commit used this week.
- [ ] Create a separate Kotlin/JDK/Gradle repository named `query-engine-lab`.
- [ ] Write one target query that eventually requires scan, filter, hash join, and hash aggregate.
- [ ] Add a module map and one passing smoke test, then commit the skeleton.

Do not implement operators yet. The useful Week 1 result is a reproducible starting point and an
explicit picture of the engine layers, not a rushed pile of code.

## Daily schedule

| Day | Work | Target |
| --- | --- | --- |
| Mon | Index Concurrency lecture + closed-book recall | 50-60 min |
| Tue | B-tree textbook + Lehman/Yao + CedarDB + latch trace | 55-65 min |
| Wed | Sorting/Aggregation lecture + closed-book recall | 50-60 min |
| Thu | Sorting textbook + paper + DuckDB engineering articles | 55-65 min |
| Fri | Join Algorithms lecture + closed-book recall | 50-60 min |
| Sat | Join textbook + thirteen joins paper + Cockroach article + project overview | 60-70 min |
| Sun | Cost calculations + DuckDB practicum + project skeleton + synthesis | 70-85 min |

If the week runs long, shorten each paper to its abstract, mechanism, results, and conclusion. Do
not skip the latch trace, cost calculations, or plan inspection.
