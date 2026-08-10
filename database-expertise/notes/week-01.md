# Week 1 Notes: Index Concurrency, Sorting, Aggregation, and Joins

This is the source of truth for Week 1 learning. The
[weekly plan](../week-01-index-concurrency-sorting-joins.md) tracks completed resources;
[progress.md](../progress.md) tracks the high-level streak and evidence.

## Daily log

| Date | Minutes | Material | One-sentence retrieval |
| --- | ---: | --- | --- |
| Aug 10 |  |  |  |
| Aug 11 |  |  |  |
| Aug 12 |  |  |  |
| Aug 13 |  |  |  |
| Aug 14 |  |  |  |
| Aug 15 |  |  |  |
| Aug 16 |  |  |  |

## Lecture 10 -- Index concurrency

### Closed-book recall

Write five bullets before reopening the slides:

1.
2.
3.
4.
5.

### Latch trace

For lookup, safe insert, and splitting insert, record:

- pages held;
- latch mode;
- safe-node decision;
- release point;
- what concurrent structural change would invalidate the traversal.

### Lehman/Yao paper card

- **Problem:**
- **Mechanism:**
- **Correctness invariant:**
- **Evidence or argument:**
- **Limitation / open question:**

### Practical connection

Explain the difference between classic crabbing, B-link recovery through right links, and
optimistic lock coupling.

## Lecture 11 -- Sorting and aggregation

### Closed-book recall

1.
2.
3.
4.
5.

### External-sort calculation

- `N = 10,000` pages:
- `B = 101` buffers:
- Initial runs:
- Merge fan-in:
- Merge passes:
- Total page I/O:
- Can the final pass be pipelined here? Why?

### Sorting paper card

- **Problem:**
- **Mechanisms worth remembering:**
- **Resource bottleneck:**
- **Most surprising implementation detail:**
- **Limitation / open question:**

### Sort vs. hash aggregation

| Condition | Prefer sorting because... | Prefer hashing because... |
| --- | --- | --- |
| Input already ordered |  |  |
| Few groups |  |  |
| Many groups |  |  |
| Tight memory |  |  |
| Output needs ordering |  |  |

## Lecture 12 -- Join algorithms

### Closed-book recall

1.
2.
3.
4.
5.

### Cost calculations

- Block nested-loop join inputs and chosen outer:
- Block nested-loop page I/O:
- Idealized Grace hash join page I/O:
- Assumptions behind the Grace estimate:
- How skew changes the result:

### Thirteen-joins paper card

- **Question tested:**
- **Algorithm families:**
- **Workload / hardware:**
- **Main result:**
- **Result that should not be generalized:**

### Join decision table

| Situation | Candidate algorithm | Reason |
| --- | --- | --- |
| Tiny indexed outer input |  |  |
| Large equi-join, one side fits memory |  |  |
| Large equi-join, neither side fits memory |  |  |
| Both inputs already ordered |  |  |
| Non-equality predicate |  |  |
| Severe key skew |  |  |

## DuckDB practicum

For each query, predict before running it.

| Query | Predicted operator | Observed operator | State retained | Prediction error |
| --- | --- | --- | --- | --- |
| `ORDER BY` |  |  |  |  |
| `GROUP BY` |  |  |  |  |
| Equi-join |  |  |  |  |

## Final synthesis

For latch crabbing, external sort, hash aggregation, and hash join:

- What invariant makes it correct?
- What resource limits it?
- What workload makes it a poor choice?
- What observation would make you choose a different mechanism?

## Parking lot

Record tangents here. Do not switch the week's sequence to pursue them.

- No tangents recorded yet.

## Retrospective

- **What can I now explain without notes?**
- **Where did my prediction differ from the observed behavior?**
- **Which concept needs retrieval again next week?**
- **What should change about the Week 2 schedule, if anything?**
