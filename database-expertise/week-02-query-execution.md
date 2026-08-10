# Week 2: Query Execution I and II

**Dates:** August 17-23, 2026

**Budget:** About 7 hours total, 45-90 minutes per day

**Theme:** How a physical plan becomes running work

The goal is not to memorize operator names. By the end of the week, you should be able to draw how
data and control move through a query plan, identify where pipelines break, and explain how
vectorization and parallelism change the cost model. The project milestone builds the batches,
types, and data-source contract that later physical operators will consume.

Write recall, paper notes, predictions, experiment results, and the retrospective in
[notes/week-02.md](notes/week-02.md). Use this file only to check off completed work.

## Definition of done

- [ ] Watch both lectures and write five bullets from memory after each.
- [ ] Complete the scoped textbook, paper, and blog reading for both topics.
- [ ] Draw one query plan and label pull/push direction, pipeline breakers, parallel boundaries, and
  state owned by each operator.
- [ ] Implement Arrow-backed types and a projected CSV scan in the query-engine lab, with tests.
- [ ] Record the project commit plus one design prediction that was right and one that was wrong.
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

## Query-engine project -- Milestone 1

Read the load-bearing sections while implementing:

- [ ] [Apache Arrow](https://howqueryengineswork.com/02-apache-arrow.html)
- [ ] [Type System](https://howqueryengineswork.com/03-type-system.html)
- [ ] [Data Sources](https://howqueryengineswork.com/04-data-sources.html)

In the separate `query-engine-lab` repository:

1. Define the supported scalar types, fields, schema, and record-batch representation on top of
   Apache Arrow.
2. Define a `DataSource` contract with `schema()` and projected `scan(...)`.
3. Implement a CSV source that returns more than one batch and materializes only requested columns.
4. Test schema discovery, projection order, null handling, and multi-batch scans.
5. Record one choice about batch size or ownership that the physical operator layer will inherit.

Do not add SQL, joins, aggregates, parallelism, or a general optimizer yet. A narrow, tested data
path is the milestone.

### Optional comparison lab

If the core week finishes early, use the
[BusTub Web Shell](https://15445.courses.cs.cmu.edu/fall2025/bustub/) or
[DuckDB EXPLAIN](https://duckdb.org/docs/current/guides/meta/explain.html) to compare one real plan
with the future module map for the lab. This does not replace the tested project milestone.

### Durable output

In [notes/week-02.md](notes/week-02.md), write 300-500 words or draw one annotated diagram
answering:

> How does data move through this plan, where can work overlap, and where must the engine
> synchronize or retain state?

Mark the evidence complete in [progress.md](progress.md) after the artifact exists.

## Daily schedule

| Day | Work | Target |
| --- | --- | --- |
| Mon | Query Execution I lecture + five bullets from memory | 50-60 min |
| Tue | Textbook sections 15.1-15.3, 15.7 + Cockroach article | 50-60 min |
| Wed | Volcano scoped read + retrieval questions | 45-55 min |
| Thu | Query Execution II lecture + five bullets from memory | 50-60 min |
| Fri | Chapter 22 scoped read + Chroma article | 50-60 min |
| Sat | Morsel-driven scoped read + book Chapters 2-4 | 60-75 min |
| Sun | Project implementation, tests, diagram, and teach-back | 75-90 min |

If a day is overloaded, do the first 20 minutes and write one retrieval sentence. Resume the same
item the next day; do not replace it with a new topic.
