# Week 1: Query Execution I and II

**Dates:** August 10-16, 2026

**Budget:** About 6 hours total, 45-60 minutes per day

**Theme:** How a physical plan becomes running work

The goal is not to memorize operator names. By the end of the week, you should be able to draw how
data and control move through a query plan, identify where pipelines break, and explain how
vectorization and parallelism change the cost model.

## Definition of done

- [ ] Watch both lectures and write five bullets from memory after each.
- [ ] Complete the scoped textbook, paper, and blog reading for both topics.
- [ ] Draw one query plan and label pull/push direction, pipeline breakers, parallel boundaries, and
  state owned by each operator.
- [ ] Run the practicum and record one prediction that was right and one that was wrong.
- [ ] Give a five-minute explanation without notes: "How does a DBMS turn a physical query plan into
  results?"

## Topic 1 -- Query execution models

### Lecture

- [ ] **CMU 15-445 #13: Query Execution I**
  - [Public Fall 2025 video](https://www.youtube.com/watch?v=E-UUd6cB57w&list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5&index=13)
  - [Spring 2026 slides](https://15445.courses.cs.cmu.edu/spring2026/slides/13-queryexecution1.pdf)
  - [Spring 2026 notes](https://15445.courses.cs.cmu.edu/spring2026/notes/13-queryexecution1.pdf)
  - **Category:** Foundational, Mental Model
  - **Why:** Establishes the operator model: iterator/pull execution, materialization, vectorized
    batches, processing direction, access methods, and expression evaluation.
  - **Time:** About 50 minutes at 1.25-1.5x, pausing only to capture questions.

### Textbook

- [ ] **Database System Concepts, 7th ed. -- Sections 15.1-15.3 and 15.7**
  - [Book site](https://www.db-book.com/)
  - **Category:** Foundational
  - **Why:** Supplies the ordered vocabulary that practice-first learning often leaves implicit:
    query-processing steps, measures of query cost, selection, and evaluation of expressions.
  - **Scope:** Read for operator contracts and cost categories; do not copy algorithms already
    understood.
  - **Time:** 30 minutes. Use the CMU notes above as the open-access fallback.

### Paper

- [ ] **Volcano -- An Extensible and Parallel Query Evaluation System** -- Goetz Graefe
  - [Open PDF](https://cs-people.bu.edu/mathan/reading-groups/papers-classics/volcano.pdf)
  - **Category:** Historical Context, Pattern Recognition
  - **Why:** The iterator model and exchange operator are not just jargon; they are reusable
    abstractions that shaped decades of execution engines.
  - **Scope:** Read the abstract, introduction, iterator/operator interface, exchange/parallelism
    discussion, and conclusion. Skim implementation detail on the first pass.
  - **Time:** 35 minutes.

### Engineering article

- [ ] **How We Built a Vectorized Execution Engine** -- Cockroach Labs
  - [Article](https://www.cockroachlabs.com/blog/how-we-built-a-vectorized-execution-engine/)
  - **Category:** Practical, Pattern Recognition
  - **Why:** Shows why a clean row-at-a-time model becomes expensive on analytical workloads and how
    batching, column-oriented processing, and code generation change real operator code.
  - **Time:** 25 minutes.

### Retrieval questions

Answer without reopening the material:

1. What state does an iterator operator own between calls to `next()`?
2. Which operators can pipeline tuples, and which must consume all input first?
3. Why can vectorized execution improve CPU efficiency even when storage remains row-oriented?
4. What does the execution model make easy, and what does it make expensive?

## Topic 2 -- Parallel query execution

### Lecture

- [ ] **CMU 15-445 #14: Query Execution II**
  - [Public Fall 2025 video](https://www.youtube.com/watch?v=Kzf1hGjtZOU&list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5&index=14)
  - [Spring 2026 slides](https://15445.courses.cs.cmu.edu/spring2026/slides/14-queryexecution2.pdf)
  - [Spring 2026 notes](https://15445.courses.cs.cmu.edu/spring2026/notes/14-queryexecution2.pdf)
  - **Category:** Foundational, Pattern Recognition
  - **Why:** Connects process models, inter-query and intra-query parallelism, exchange operators,
    partitioning, and bushy execution to the mechanisms from the first lecture.
  - **Time:** About 50 minutes at 1.25-1.5x.

### Textbook

- [ ] **Database System Concepts, 7th ed. -- Chapter 22**
  - [Book site](https://www.db-book.com/)
  - **Category:** Foundational
  - **Why:** Provides the structured treatment of parallel architectures, data partitioning,
    operator parallelism, and coordination costs.
  - **Scope:** Prioritize parallel query evaluation and intra-query parallelism. Skim material that
    duplicates the lecture.
  - **Time:** 30 minutes. Use the CMU notes above as the open-access fallback.

### Paper

- [ ] **Morsel-Driven Parallelism: A NUMA-Aware Query Evaluation Framework for the Many-Core Age**
  -- Leis, Boncz, Kemper, and Neumann
  - [Open PDF](https://db.in.tum.de/~leis/papers/morsels.pdf)
  - **Category:** Pattern Recognition, Practical
  - **Why:** Replaces a fixed degree of parallelism with small schedulable units of work, tying
    execution to load balancing, NUMA locality, and interference between concurrent queries.
  - **Scope:** Read the abstract, motivation, morsel scheduler design, NUMA discussion, evaluation
    summary, and conclusion.
  - **Time:** 35 minutes.

### Engineering article

- [ ] **Designing a Query Execution Engine** -- Chroma
  - [Article](https://www.trychroma.com/engineering/execution-engine)
  - **Category:** Practical, Mental Model
  - **Why:** A modern implementation account of push vs. pull execution, morsel-driven scheduling,
    interruptibility, and dynamically changing parallelism under contention.
  - **Time:** 25 minutes.

### Retrieval questions

1. What is the difference between inter-query, inter-operator, and intra-operator parallelism?
2. What does an exchange boundary do to data ownership, buffering, and backpressure?
3. Why does fixed partitioning struggle with skew and changing resource availability?
4. How do NUMA locality and work stealing pull in different directions?

## Practicum -- Predict, inspect, explain

Use the [BusTub Web Shell](https://15445.courses.cs.cmu.edu/fall2025/bustub/) for plan structure and
a local [DuckDB](https://duckdb.org/install/) installation for the thread comparison. The
[DuckDB EXPLAIN guide](https://duckdb.org/docs/current/guides/meta/explain.html) and
[TPC-H extension documentation](https://duckdb.org/docs/current/core_extensions/tpch.html) are
references, not extra reading assignments.

### Part A -- Read plans before measuring

In BusTub, run `EXPLAIN` for:

1. a scan with a filter;
2. an aggregate with `GROUP BY`;
3. a join with a filter on one input.

Before executing each query, write down:

- the operator tree you expect;
- where tuples can stream;
- where the engine must retain state;
- which operator is likely to dominate CPU or memory.

### Part B -- Change the parallelism

In DuckDB:

```sql
INSTALL tpch;
LOAD tpch;
CALL dbgen(sf = 0.1);

SET threads = 1;
EXPLAIN ANALYZE
SELECT c_mktsegment, count(*) AS order_count
FROM customer
JOIN orders ON c_custkey = o_custkey
GROUP BY c_mktsegment
ORDER BY c_mktsegment;

SET threads = 4;
EXPLAIN ANALYZE
SELECT c_mktsegment, count(*) AS order_count
FROM customer
JOIN orders ON c_custkey = o_custkey
GROUP BY c_mktsegment
ORDER BY c_mktsegment;
```

Record the `HASH_JOIN`, aggregation, scan, and ordering operators. Compare timings, but do not treat
one run as a benchmark. Explain why more threads may help little at this scale and name the costs
that could dominate.

### Durable output

Write 300-500 words or draw one annotated diagram answering:

> How does data move through this plan, where can work overlap, and where must the engine
> synchronize or retain state?

Link or describe the artifact in [progress.md](progress.md).

## Daily schedule

| Day | Work | Target |
| --- | --- | --- |
| Mon | Query Execution I lecture + five bullets from memory | 50-60 min |
| Tue | Textbook sections 15.1-15.3, 15.7 + Cockroach article | 50-60 min |
| Wed | Volcano scoped read + retrieval questions | 45-55 min |
| Thu | Query Execution II lecture + five bullets from memory | 50-60 min |
| Fri | Chapter 22 scoped read + Chroma article | 50-60 min |
| Sat | Morsel-driven scoped read + retrieval questions | 45-55 min |
| Sun | BusTub/DuckDB practicum + teach-back | 60 min |

If a day is overloaded, do the first 20 minutes and write one retrieval sentence. Resume the same
item the next day; do not replace it with a new topic.
