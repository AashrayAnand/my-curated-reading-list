# Database Expertise Track

This track is for turning existing database experience into systematic, transferable judgment.
It begins with the largest current gap -- query execution and optimization -- and then moves
serially through the remainder of CMU 15-445/645 before entering CMU 15-721.

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
3. **Apply** -- inspect a plan, run an experiment, implement a small mechanism, or analyze a real
   system.
4. **Explain** -- produce a short durable artifact: diagram, paper review, benchmark note, or
   teach-back.
5. **Revisit** -- re-derive the idea one and four weeks later.

An hour a day is enough for this loop if the reading is scoped. It is not enough to watch two full
lectures, read two complete papers, read two complete chapters, and build a project every week.
The weekly files therefore identify the load-bearing sections and preserve the application block.

## Focus contract

- **Single track:** From August 10 through October 4, 2026, pause unrelated AI, systems, and general
  weekly-list material. Database-adjacent OS or hardware material is allowed only when it explains
  the current database topic.
- **Close the current thread:** If the disk-I/O path is unfinished, spend at most the first two
  sessions closing the current section. Do not postpone query execution for another prerequisite.
- **No mid-sprint syllabus redesign:** Follow the sequence below. Put interesting tangents in
  `context/backlog.md` instead of switching tracks.
- **Minimum viable day:** Twenty focused minutes plus one sentence written from memory counts.
  Missing a day does not reset the plan; resume at the next block.
- **Protect application:** If the week is overloaded, narrow a paper to its core sections. Do not
  remove the practicum or teach-back.
- **Weekly gate:** Do not advance merely because the links were opened. Advance after producing the
  week's evidence in [progress.md](progress.md).

## Phase 1 -- Eight-week bridge sprint

Use the public [Fall 2025 lecture playlist](https://www.youtube.com/playlist?list=PLSE8ODhjZXjYMAgsGH-GtY5rJYZ6zjsd5)
with the latest public [Spring 2026 slides and notes](https://15445.courses.cs.cmu.edu/spring2026/schedule.html).

| Week | Dates | Two lecture units | Capability target |
| --- | --- | --- | --- |
| 1 | Aug 10-16 | Query Execution I + II | Explain iterator, materialization, vectorized, and parallel execution; identify pipeline breakers and sources of coordination overhead. |
| 2 | Aug 17-23 | Query Planning & Optimization I + II | Trace SQL to logical and physical plans; explain cardinality, cost models, plan enumeration, and why estimates fail. |
| 3 | Aug 24-30 | Concurrency Control Theory + Two-Phase Locking | Derive serializability conflicts and reason about lock granularity, deadlocks, and strict 2PL. |
| 4 | Aug 31-Sep 6 | Timestamp Ordering + MVCC I | Compare pessimistic and timestamp-based ordering; explain version visibility and validation. |
| 5 | Sep 7-13 | MVCC II + Database Logging | Connect version management to WAL, durability, checkpoints, and write ordering. |
| 6 | Sep 14-20 | Database Recovery + Distributed Databases I | Explain ARIES-style recovery phases and the architectural choices behind distributed execution. |
| 7 | Sep 21-27 | Distributed Databases II + Systems Potpourri | Reason about partitioning, replication, distributed transactions, and where local DBMS assumptions break. |
| 8 | Sep 28-Oct 4 | Repair Week: Sorting & Aggregation Algorithms + Join Algorithms | Backfill the two immediately preceding 15-445 units identified as weak spots; compare algorithms by memory, I/O, ordering, skew, and workload. |

The detailed first week is in
[week-01-query-execution.md](week-01-query-execution.md). Companion readings for later weeks should
be curated one week at a time. Fixing the lecture sequence now removes decision fatigue; delaying
the paper and blog choices prevents a large speculative syllabus from becoming another form of
procrastination.

## Phase 2 -- Advanced course progression

Week 8 is the only intentional step backward in the lecture order. It repairs the sorting and join
algorithm gap after the serial pass reaches the end of 15-445, so the advanced execution material
does not rest on a known weak prerequisite.

After the eight-week checkpoint, continue with the public
[CMU 15-721 Spring 2024 playlist](https://www.youtube.com/playlist?list=PLSE8ODhjZXjYa_zX-KeMJui7pcN1rIaIJ)
at two substantive lectures per week:

| Week | Advanced lecture pair |
| --- | --- |
| 9 | Modern OLAP Database Systems + Data Formats & Encoding I |
| 10 | Data Formats & Encoding II + Query Execution & Processing I |
| 11 | Query Execution & Processing II + Vectorized Query Execution Using SIMD |
| 12 | JIT Query Compilation & Code Generation + Query Scheduling & Coordination |
| 13 | Parallel Hash Join Algorithms + Multi-Way / Worst-Case Optimal Joins |
| 14 | User-Defined Function Optimizations + Database Networking Protocols |
| 15 | Query Optimizer Implementation I + II |
| 16 | Query Optimizer Implementation III + Google BigQuery / Dremel |
| 17 | Databricks Photon / Spark SQL + Snowflake Internals |
| 18 | DuckDB + Yellowbrick |
| 19 | Amazon Redshift + advanced-course synthesis and capstone selection |

Use the [Fall 2025 advanced course reading schedule](https://www.cs.cmu.edu/~15721-f25/schedule.html)
as the paper bank. Where the 2024 video and 2025 paper sequence differ, keep the video order fixed
and select the paper that best reinforces that week's mechanism.

## Phases 3 and 4 -- Converting knowledge into expert judgment

After the course sequence:

- **Three-month capstone:** Build or extend one small query-processing system. A focused BusTub
  subset, a DuckDB extension, or a standalone executor is enough. The goal is to make operator
  interfaces, memory ownership, scheduling, costing, and measurement concrete -- not to build a
  production DBMS.
- **Three-month specialization:** Choose one area where professional experience and curiosity
  overlap: execution/optimization, transactions/recovery, storage, or distributed databases.
  Read one important paper per week, reproduce one result per month, and publish one synthesis per
  month.
- **Ongoing breadth maintenance:** Once the focused year is complete, rotate through the other
  database areas quarterly so depth does not become tunnel vision.

A realistic one-year outcome is not "knows every database." It is: **can enter an unfamiliar
database subsystem, derive the important trade-offs, test the model with evidence, and communicate
the result clearly.**
