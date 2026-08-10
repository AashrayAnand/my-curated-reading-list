# Six-Week Query Engine Project

This project is the application spine for the first six weeks of the database expertise track.
The goal is not to produce a production database. It is to make query planning and execution
mechanisms concrete enough to implement, test, inspect, and explain.

Use [How Query Engines Work](https://howqueryengineswork.com/01-what-is-a-query-engine.html) as the
build-along guide and the
[KQuery companion repository](https://github.com/andygrove/how-query-engines-work) as a reference.
The current companion implementation is Kotlin, uses Apache Arrow, and is
[Apache-2.0 licensed](https://github.com/andygrove/how-query-engines-work/blob/main/LICENSE).
This build-along supplements the CMU lectures, textbook sections, papers, and engineering articles;
it does not replace the theory track.

## Project contract

- Create a separate repository named `query-engine-lab`; this reading-list repository stores only
  the plan and learning evidence.
- Use Kotlin, JDK 11+, Gradle, and Apache Arrow for this pass. Matching the book removes language
  translation as a source of delay. A Rust port is a separate future project.
- Start from a small blank skeleton rather than counting a KQuery fork as implementation. Consult
  KQuery after making an initial design attempt, then record what changed.
- Pin and record the KQuery `main` commit used at kickoff. Do not chase upstream changes during the
  six weeks.
- Protect two coding blocks per week. If time runs short, narrow a secondary article or paper;
  never replace the tested milestone with more reading.
- End every week with a passing test command, one commit, and a short note connecting code to the
  lecture model.

## Scope

The core engine may support only:

- Arrow-backed scalar types, schemas, and record batches;
- CSV input with projection;
- logical expressions and plans;
- a DataFrame-style builder and a small SQL subset;
- pull-based physical scan, projection, filter, limit, hash aggregate, and hash join;
- logical-to-physical planning;
- two visible optimizer rewrites;
- one bounded parallel execution boundary;
- correctness tests and one reproducible benchmark.

Explicitly exclude storage management, buffer pools, indexes, transactions, recovery, catalog
durability, a complete SQL grammar, networking, and production hardening. Read the distributed
chapter for the model; a networked implementation is not required.

## Target query

Choose one small two-table query in Week 1 and keep it fixed as the acceptance target:

```sql
SELECT customer_segment, COUNT(*)
FROM customers
JOIN orders ON customers.customer_id = orders.customer_id
WHERE orders.total > 100
GROUP BY customer_segment;
```

It should fail as an end-to-end test at first. Each milestone supplies another layer until it
passes in Week 5. Adapt identifiers to the lab's SQL subset, but do not keep changing the workload.

## Weekly milestones

| Week | Book sections | Minimum implementation evidence |
| --- | --- | --- |
| 1 | [The KQuery Project](https://howqueryengineswork.com/00-source-code.html) + [What Is a Query Engine?](https://howqueryengineswork.com/01-what-is-a-query-engine.html) | Buildable skeleton, pinned reference commit, module map, target query, passing smoke test |
| 2 | [Apache Arrow](https://howqueryengineswork.com/02-apache-arrow.html) + [Type System](https://howqueryengineswork.com/03-type-system.html) + [Data Sources](https://howqueryengineswork.com/04-data-sources.html) | Types, schema, batches, projected CSV scan, null and multi-batch tests |
| 3 | [Logical Plans](https://howqueryengineswork.com/05-logical-plan.html) + [DataFrames](https://howqueryengineswork.com/06-dataframe.html) + [SQL Support](https://howqueryengineswork.com/07-sql-support.html) | Expressions, logical plans, DataFrame builder, minimal SQL-to-logical-plan tests |
| 4 | [Physical Plans](https://howqueryengineswork.com/08-physical-plan.html) + [Query Planning](https://howqueryengineswork.com/09-query-planner.html) + [Joins](https://howqueryengineswork.com/10-joins.html) | Pull-based operators, hash aggregate, hash join, planner, DataFrame acceptance path |
| 5 | [Subqueries](https://howqueryengineswork.com/11-subqueries.html) + [Query Optimizers](https://howqueryengineswork.com/12-optimizations.html) + [Query Execution](https://howqueryengineswork.com/13-execution.html) | Two optimizer rules with before/after plans, execution context, passing SQL acceptance query |
| 6 | [Parallel Execution](https://howqueryengineswork.com/14-parallel-query.html) + [Distributed Execution](https://howqueryengineswork.com/15-distributed-query.html) + [Testing](https://howqueryengineswork.com/16-testing.html) + [Benchmarks](https://howqueryengineswork.com/17-benchmarks.html) | One bounded parallel boundary, full correctness suite, 1-vs-N-worker benchmark, final architecture note |

The milestone is the minimum. Do not add stretch work until its tests pass.

## Weekly evidence

Record this in `notes/week-NN.md`:

1. reference chapter and KQuery modules inspected;
2. lab commit hash;
3. exact test command and result;
4. one invariant encoded by the implementation;
5. one prediction that differed from the result;
6. one diagram or plan before/after snapshot;
7. the next narrow milestone.

The project counts as learning only when the evidence exists. Lines of code and time spent are not
completion criteria.

## Week 6 completion gate

The six-week project is complete when:

- the fixed SQL query passes end to end;
- logical and physical plans can be printed and explained;
- filter/projection pushdown or equivalent rewrite is visible in a before/after plan;
- hash join and hash aggregate have correctness tests covering duplicates and empty inputs;
- the parallel path produces the same results as the single-worker path;
- the benchmark records workload, data size, worker count, warmup, repetitions, and raw timings;
- the final note explains what the engine cannot do and why those omissions were intentional.

At that point, stop. Decide from the evidence whether a later project should deepen execution,
port the engine, or move into storage and indexing. Do not make that decision mid-sprint.
