# Week 2 Notes: Query Execution I and II

This is the source of truth for Week 2 learning. The
[weekly plan](../week-02-query-execution.md) tracks completed resources;
[progress.md](../progress.md) tracks the high-level streak and evidence.

## Daily log

| Date | Minutes | Material | One-sentence retrieval |
| --- | ---: | --- | --- |
| Aug 17 |  |  |  |
| Aug 18 |  |  |  |
| Aug 19 |  |  |  |
| Aug 20 |  |  |  |
| Aug 21 |  |  |  |
| Aug 22 |  |  |  |
| Aug 23 |  |  |  |

## Lecture 13 -- Query Execution I

### Closed-book recall

1.
2.
3.
4.
5.

### Execution-model comparison

| Model | Unit of data | Control flow | State retained | Main advantage | Main cost |
| --- | --- | --- | --- | --- | --- |
| Iterator / pull |  |  |  |  |  |
| Materialized |  |  |  |  |  |
| Vectorized |  |  |  |  |  |
| Push-based |  |  |  |  |  |

### Volcano paper card

- **Problem:**
- **Operator interface:**
- **Role of exchange:**
- **Reusable design pattern:**
- **Limitation / open question:**

### Vectorized-engine connection

- Row-at-a-time overhead:
- Why batches help:
- Role of columnar layout:
- Workloads where vectorization helps less:

## Lecture 14 -- Query Execution II

### Closed-book recall

1.
2.
3.
4.
5.

### Parallelism comparison

| Form | Unit being parallelized | Coordination point | Main source of skew or overhead |
| --- | --- | --- | --- |
| Inter-query |  |  |  |
| Inter-operator |  |  |  |
| Intra-operator |  |  |  |

### Morsel-driven paper card

- **Problem:**
- **Scheduling mechanism:**
- **NUMA strategy:**
- **Evidence:**
- **Trade-off / limitation:**

### Plan diagram

Draw one query plan and label:

- pull or push direction;
- pipeline breakers;
- state owned by each operator;
- exchange or parallel boundaries;
- buffering and backpressure points.

## Query-engine project -- Milestone 1

### Chapter-to-code map

| Book concept | Lab type or module | Invariant to preserve |
| --- | --- | --- |
| Arrow batch |  |  |
| Scalar type and field |  |  |
| Schema |  |  |
| Data source |  |  |
| Projected scan |  |  |

### Implementation evidence

- **Supported scalar types:**
- **Batch-size decision:**
- **Ownership / lifetime decision:**
- **CSV projection behavior:**
- **Null behavior:**
- **Multi-batch test:**
- **Full test command and result:**
- **Lab commit:**

### Prediction check

- **Prediction that held:**
- **Prediction that failed:**
- **What changed in the design afterward:**

### Optional external comparison

- **Engine and query:**
- **Observed plan:**
- **How it differs from the lab's future module map:**

## Final synthesis

Write 300-500 words or attach an annotated diagram answering:

> How does data move through this plan, where can work overlap, and where must the engine
> synchronize or retain state?

## Parking lot

Record tangents here. Do not switch the week's sequence to pursue them.

- No tangents recorded yet.

## Retrospective

- **What can I now explain without notes?**
- **Where did my prediction differ from the observed behavior?**
- **Which execution model or parallelism trade-off remains fuzzy?**
- **What should change about the Week 3 schedule, if anything?**
