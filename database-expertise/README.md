# Database Expertise Track

This track is for turning existing database experience into systematic, transferable judgment.
It begins with the prerequisite gap in index latching, sorting, aggregation, and joins, then spends
six weeks making query execution and planning concrete through a small query-engine build. The
remaining CMU 15-445/645 and 15-721 topics follow after that focused block.

Two months will not make anyone "finished" as a database expert. It can close important gaps,
build a durable learning system, and create evidence that theory is changing how you reason.

## What expertise means

Expertise is not the amount of material consumed. It is the ability to:

- explain the important models from memory and connect them to real systems;
- predict how a design or query will behave before running it;
- measure what actually happened and revise the prediction;
- choose between alternatives by naming the workload, invariants, and trade-offs;
- transfer an idea from a paper into a new context instead of merely recognizing its name;
- teach the idea clearly enough that gaps in understanding become visible.

For databases, that means being able to trace a query from SQL through planning, operators,
storage, concurrency control, logging, recovery, and distributed execution. It also means knowing
where that model stops applying and what evidence would settle an uncertain design question.

## The learning loop

Every topic follows the same loop:

1. **Absorb** -- lecture, textbook chapter, paper, and practical engineering article.
2. **Retrieve** -- close the material and write the model from memory.
3. **Apply** -- inspect a plan, run an experiment, or implement the week's query-engine milestone.
4. **Explain** -- produce a short durable artifact: diagram, paper review, benchmark note, or
   teach-back.
5. **Revisit** -- re-derive the idea one and four weeks later.

An hour a day is enough for this loop if the reading is scoped. It is not enough to watch every
lecture, read every paper end to end, and build a project every week. The weekly files therefore
identify the load-bearing sections and preserve the application block.

## Where to annotate progress

Use three layers, each with one job:

1. **Weekly plan (`week-NN-*.md`):** Check off resources and exercises. Do not put long notes here.
2. **Weekly notes (`notes/week-NN.md`):** Write closed-book recall, paper cards, calculations,
   experiment results, the project commit and test evidence, synthesis, and the retrospective.
   This is the durable learning artifact.
3. **Progress ledger (`progress.md`):** Mark the streak and link to evidence only after the weekly
   notes contain it.

This keeps planning separate from thinking. When returning months later, read the notes file, not
the checklist.

## Focus contract

- **Single track:** From August 10 through October 4, 2026, pause unrelated AI, systems, and general
  weekly-list material. Database-adjacent OS or hardware material is allowed only when it explains
  the current database topic.
- **Close the current thread:** If the disk-I/O path is unfinished, spend at most the first two
  sessions closing the current section. Do not postpone the database sprint for another
  prerequisite.
- **No mid-sprint syllabus redesign:** Follow the sequence below. Put interesting tangents in
  `context/backlog.md` instead of switching tracks.
- **Minimum viable day:** Twenty focused minutes plus one sentence written from memory counts.
  Missing a day does not reset the plan; resume at the next block.
- **Protect application:** If the week is overloaded, narrow a paper to its core sections. Do not
  remove the query-engine milestone or teach-back.
- **Keep the project narrow:** Use Kotlin and Apache Arrow for this pass so the book's code remains
  directly comparable. Do not port to another language or add storage management, indexes,
  transactions, or logging during the six-week intensive.
- **Separate repositories:** Keep the implementation in its own `query-engine-lab` repository.
  Record commit hashes and results in the weekly notes; do not turn this reading-list repository
  into the implementation repository.
- **Weekly gate:** Do not advance merely because the links were opened. Advance after producing the
  week's evidence in [progress.md](progress.md).

## Phase 1 -- Six-week query-engine intensive

Use the public [Fall 2025 lecture playlist](https://www.youtube.com/playlist?list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5)
with the latest public [Spring 2026 slides and notes](https://15445.courses.cs.cmu.edu/spring2026/schedule.html).
The coding spine is [How Query Engines Work](query-engine-project.md), paired with the book's
[Apache-2.0 companion implementation](https://github.com/andygrove/how-query-engines-work).

| Week | Dates | Theory focus | Book chapters | Tested project milestone |
| --- | --- | --- | --- | --- |
| 1 | Aug 10-16 | Index concurrency + sorting/aggregation + joins | KQuery Project + What Is a Query Engine? | Buildable skeleton, target query, architecture map, smoke test |
| 2 | Aug 17-23 | Query Execution I + II | Apache Arrow + Type System + Data Sources | Arrow-backed types and projected CSV scan |
| 3 | Aug 24-30 | Query Planning & Optimization I + II | Logical Plans + DataFrames + SQL Support | Expressions, logical plans, DataFrame API, minimal SQL-to-logical-plan path |
| 4 | Aug 31-Sep 6 | Physical operators and algorithm selection | Physical Plans + Query Planning + Joins | Pull-based scan/filter/projection, hash aggregate, hash join, physical planner |
| 5 | Sep 7-13 | Optimizer rules and end-to-end execution | Subqueries + Query Optimizers + Query Execution | Two observable rewrite rules and an end-to-end SQL execution path |
| 6 | Sep 14-20 | Parallel execution and measurement | Parallel + Distributed Execution + Testing + Benchmarks | One bounded parallel boundary, correctness suite, benchmark, final architecture write-up |

The detailed plans are:

- [Week 1: Index Concurrency, Sorting, Aggregation, and Joins](week-01-index-concurrency-sorting-joins.md)
- [Week 2: Query Execution I and II](week-02-query-execution.md)
- [Six-Week Query Engine Project](query-engine-project.md)

Companion readings for later weeks should be curated one week at a time. Fixing the lecture
sequence now removes decision fatigue; delaying later paper and blog choices prevents a large
speculative syllabus from becoming another form of procrastination.

## Phase 2 -- Resume the intro course

After the query-engine checkpoint, return to the transaction, recovery, and distributed-systems
sequence. The first two rows complete the original August 10-October 4 focus block; continue only
after the Week 8 retrospective.

| Week | Lecture units | Capability target |
| --- | --- | --- |
| 7 | Concurrency Control Theory + Two-Phase Locking | Derive serializability conflicts and reason about lock granularity, deadlocks, and strict 2PL. |
| 8 | Timestamp Ordering + MVCC I | Compare pessimistic and timestamp-based ordering; explain version visibility and validation. |
| 9 | MVCC II + Database Logging | Connect version management to WAL, durability, checkpoints, and write ordering. |
| 10 | Database Recovery + Distributed Databases I | Explain ARIES-style recovery phases and the architectural choices behind distributed execution. |
| 11 | Distributed Databases II + Systems Potpourri | Reason about partitioning, replication, distributed transactions, and where local DBMS assumptions break. |

## Phase 3 -- Advanced course progression

After completing the intro sequence, continue with the public
[CMU 15-721 Spring 2024 playlist](https://www.youtube.com/playlist?list=PLSE8ODhjZXjYa_zX-KeMJui7pcN1rIaIJ)
at two substantive lectures per week. Treat this as a topic bank: execution or optimizer lectures
used as companions during Weeks 4-6 do not need to be repeated unless the project evidence exposes
a gap.

| Block | Advanced lecture pair |
| --- | --- |
| 1 | Modern OLAP Database Systems + Data Formats & Encoding I |
| 2 | Data Formats & Encoding II + Query Execution & Processing I |
| 3 | Query Execution & Processing II + Vectorized Query Execution Using SIMD |
| 4 | JIT Query Compilation & Code Generation + Query Scheduling & Coordination |
| 5 | Parallel Hash Join Algorithms + Multi-Way / Worst-Case Optimal Joins |
| 6 | User-Defined Function Optimizations + Database Networking Protocols |
| 7 | Query Optimizer Implementation I + II |
| 8 | Query Optimizer Implementation III + Google BigQuery / Dremel |
| 9 | Databricks Photon / Spark SQL + Snowflake Internals |
| 10 | DuckDB + Yellowbrick |
| 11 | Amazon Redshift + advanced-course synthesis |

Use the [Fall 2025 advanced course reading schedule](https://www.cs.cmu.edu/~15721-f25/schedule.html)
as the paper bank. Where the 2024 video and 2025 paper sequence differ, keep the video order fixed
and select the paper that best reinforces that week's mechanism.

## Phases 4 and 5 -- Converting knowledge into expert judgment

After the course sequence:

- **Query-engine continuation:** Use the six-week project as the capstone. Extend it only if the
  Week 6 retrospective identifies a specific unanswered question; do not automatically turn it
  into a storage engine or production DBMS.
- **Three-month specialization:** Choose one area where professional experience and curiosity
  overlap: execution/optimization, transactions/recovery, storage, or distributed databases.
  Read one important paper per week, reproduce one result per month, and publish one synthesis per
  month.
- **Ongoing breadth maintenance:** Once the focused year is complete, rotate through the other
  database areas quarterly so depth does not become tunnel vision.

A realistic one-year outcome is not "knows every database." It is: **can enter an unfamiliar
database subsystem, derive the important trade-offs, test the model with evidence, and communicate
the result clearly.**
