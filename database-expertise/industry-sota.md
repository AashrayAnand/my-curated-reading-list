# Industry State of the Art: Seminal Systems and Key Innovations

This page is the third part of the database track. The [intro course](intro-15-445.md) and the
[advanced course](advanced-15-721.md) teach the theory. This page covers the work that changed how
real systems are built. It is a "who's who" of database innovations, plus a set of case studies
of unusual and ambitious systems.

Everything here is for reading and watching. There are no projects. Every link was fetched and
checked when this page was written.

## How to use this page

- Read it at your own pace. It has no weekly schedule. Pick one theme at a time.
- Read the paper first, then the blog posts. Blogs often give the "why" and the failure stories
  that papers leave out.
- Times are rough: a paper is 45-90 minutes, a blog post is 10-30 minutes, a talk is about 60 minutes.
- Papers that already appear in the other two courses are not repeated here. Examples are
  Snowflake, Dremel, Photon, Redshift, DuckDB, Velox, and ARIES. The theme tables point to them.

## Part 1. Where to find the important work

These sources keep producing the best material. Check them once or twice a year.

| Source | What it gives you | Link |
| --- | --- | --- |
| SIGMOD Systems Award | One real, widely used system per year. Listed in Part 2. | [Winners](https://sigmod.org/sigmod-awards/sigmod-systems-award/) |
| SIGMOD Best Paper Award | Top research papers by year, with an industry category in some years. | [Recipients](https://sigmod.org/sigmod-awards/sigmod-best-paper-award/) |
| SIGMOD Test of Time Award | Papers whose impact lasted 10-12 years. Good for finding ideas that held up. | [Winners](https://sigmod.org/sigmod-awards/sigmod-test-of-time-award/) |
| VLDB conference awards | Best research, industry, and benchmark papers each year. | [VLDB 2025](https://www.vldb.org/2025/?conference-awards) |
| CMU database seminars | Talks by the engineers who build the systems. Listed in Part 5. | [All series](https://db.cs.cmu.edu/seminars/spring2026/) |
| Andy Pavlo's yearly reviews | A map of what happened in databases each year. | [2023](https://www.cs.cmu.edu/~pavlo/blog/2024/01/2023-databases-retrospective.html), [2024](https://www.cs.cmu.edu/~pavlo/blog/2025/01/2024-databases-retrospective.html), [2025](https://www.cs.cmu.edu/~pavlo/blog/2026/01/2025-databases-retrospective.html) |

Other lists that collect high-impact work:

| Source | What it gives you | Link |
| --- | --- | --- |
| Readings in Database Systems (the Red Book) | A curated set of core papers with short essays. | [redbook.io](http://www.redbook.io/) |
| Andy Pavlo's home page and CMU database group papers | Pavlo's own papers and the group's publication list. | [Home page](https://www.cs.cmu.edu/~pavlo/), [Papers](https://db.cs.cmu.edu/papers/) |
| Andy Pavlo's blog | Yearly reviews and long opinion posts. | [Blog](https://www.cs.cmu.edu/~pavlo/blog/) |
| CIDR | A conference for new ideas and systems experience. Papers are free. | [cidrdb.org](https://www.cidrdb.org/) |
| SIGMOD Research Highlights | Papers picked for their lasting value. | [List](https://sigmod.org/sigmod-awards/sigmod-research-highlights/) |
| SIGMOD Contributions and Codd Innovations awards | People and ideas honored for long impact. | [Contributions](https://sigmod.org/sigmod-awards/sigmod-contributions-award/), [Codd](https://sigmod.org/sigmod-awards/sigmod-edgar-f-codd-innovations-award/) |
| Papers We Love | A community list of papers across computer science. | [GitHub](https://github.com/papers-we-love/papers-we-love) |
| Jepsen | Independent consistency tests of real databases. | [GitHub](https://github.com/jepsen-io/jepsen) |
| Benchmarks | BenchBase from CMU, and the TPC-H spec. | [BenchBase](https://github.com/cmu-db/benchbase), [TPC-H](https://www.tpc.org/tpch/) |

### Recent award papers worth reading

These come from the recipient lists on the award pages.

| Year and award | Paper | Why read it |
| --- | --- | --- |
| VLDB 2025 Best Industry Paper | [Ursa: A Lakehouse-Native Data Streaming Engine for Kafka](https://www.vldb.org/pvldb/vol18/p5184-guo.pdf) (StreamNative) | Shows a streaming system that writes straight to lakehouse tables on object storage. |
| VLDB 2025 Best Industry Runner-Up | [Delta Sharing: An Open Protocol for Cross-Platform Data Sharing](https://www.vldb.org/pvldb/vol18/p5197-puttaswamy.pdf) (Databricks) | Explains an open protocol for sharing tables between different vendors. |
| VLDB 2025 Best Research Paper | [Diva: Dynamic Range Filter for Var-Length Keys and Queries](https://www.vldb.org/pvldb/vol18/p3923-eslami.pdf) | Extends Bloom-filter-style filtering to range queries. This matters for LSM trees. |
| VLDB 2025 Best Research Paper | [AnyBlox: A Framework for Self-Decoding Datasets](https://www.vldb.org/pvldb/vol18/p4017-gienieczko.pdf) | Proposes data files that carry their own decoder, to solve file format lock-in. |
| SIGMOD 2025 Best Paper | [LpBound: Pessimistic Cardinality Estimation using Lp-Norms of Degree Sequences](https://arxiv.org/pdf/2502.05912) (Zhang et al.) | A new way to bound join sizes. It connects to the optimizer and cost model lectures. |
| SIGMOD 2025 Best Paper | [Low-Latency Transaction Scheduling via Userspace Interrupts: Why Wait or Yield When You Can Preempt?](https://www.cs.sfu.ca/~tzwang/preemptdb.pdf) (Huang et al.) | Shows preemption inside a database for short transactions. Search the title to find the paper. |
| SIGMOD 2026 Honorable Mention | [F3: The Open-Source Data File Format for the Future](https://db.cs.cmu.edu/papers/2025/zeng-sigmod2025.pdf) (Zeng et al.) | The next step after Parquet. |

## Part 2. A who's who of systems: the SIGMOD Systems Award

The Systems Award picks one system each year that was both innovative and widely deployed. Read in
this order to see how the field moved. The year is the award year. The 2026 page lists no award.

| Year | System | What it changed | Start with |
| --- | --- | --- | --- |
| 2015 | Postgres | Extensible types, indexes, and rules. It is the base of the PostgreSQL you know. | [The Design of POSTGRES](https://dsf.berkeley.edu/papers/ERL-M85-95.pdf) (Stonebraker and Rowe, 1986) |
| 2016 | MonetDB | The column store and vectorized execution. | [MonetDB/X100](https://15721.courses.cs.cmu.edu/spring2024/papers/04-execution1/boncz-cidr2005.pdf) (already in the advanced course) |
| 2017 | SQLite | The most deployed database. It shows how far a simple embedded design can go. | [Atomic Commit In SQLite](https://www.sqlite.org/atomiccommit.html) |
| 2018 | Hive and Pig | SQL and dataflow on top of Hadoop. The start of the data lake. | [MapReduce](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf) (Dean and Ghemawat, the base that both build on) |
| 2019 | Aurora | Moved the log and storage out of the database. See Theme A below. | [Aurora, SIGMOD 2017](https://web.stanford.edu/class/cs245/readings/aurora.pdf) |
| 2020 | BerkeleyDB | The embedded key-value store that many systems used inside. | Skim the [award citation](https://sigmod.org/sigmod-awards/sigmod-systems-award/) and compare with RocksDB in Theme D. |
| 2021 | Sloan Digital Sky Survey | A science database at large scale. It shaped how data is shared. | [Award citation](https://sigmod.org/sigmod-awards/sigmod-systems-award/) |
| 2022 | Apache Spark | General-purpose distributed data processing. | [Databricks lakehouse overview](https://www.databricks.com/blog/2020/01/30/what-is-a-data-lakehouse.html), then Photon in the advanced course |
| 2023 | Apache Flink | Stateful stream processing with exactly-once results. | [Award citation](https://sigmod.org/sigmod-awards/sigmod-systems-award/) |
| 2024 | Apache SINGA | A system for machine learning workloads inside data systems. | [Award citation](https://sigmod.org/sigmod-awards/sigmod-systems-award/) |
| 2025 | Spanner | Globally distributed transactions that use synchronized clocks. | [Spanner, OSDI 2012](https://static.googleusercontent.com/media/research.google.com/en//archive/spanner-osdi2012.pdf) and the [2025 citation](https://sigmod.org/2025-sigmod-systems-award/) |

## Part 3. Seminal innovations by theme

Each theme lists the main paper, then companion posts. The order inside a theme goes from older to newer.

### Theme A. Separating storage from compute (the cloud-native database)

This is the largest change in OLTP systems in the last decade. Start here.

| Order | Read | Kind | Why it matters |
| --- | --- | --- | --- |
| 1 | [Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases](https://web.stanford.edu/class/cs245/readings/aurora.pdf) (SIGMOD 2017) | Paper | Core idea: "the log is the database." The database sends only redo records to a storage service. |
| 2 | [Amazon Aurora: On Avoiding Distributed Consensus for I/Os, Commits, and Membership Changes](https://pages.cs.wisc.edu/~yxy/cs839-s20/papers/aurora-sigmod-18.pdf) (SIGMOD 2018) | Paper | Explains how Aurora uses quorums and log sequence numbers instead of full consensus on the write path. |
| 3 | [Quorums and correlated failure](https://aws.amazon.com/blogs/database/amazon-aurora-under-the-hood-quorum-and-correlated-failure/) | Blog, part 1 of 4 | Why a 6-copy, 4-of-6 write quorum across three availability zones survives a zone failure. |
| 4 | [Quorum membership](https://aws.amazon.com/blogs/database/amazon-aurora-under-the-hood-quorum-membership/) | Blog, part 2 | How the system changes the members of a quorum while it keeps serving writes. |
| 5 | [Quorum reads and mutating state](https://aws.amazon.com/blogs/database/amazon-aurora-under-the-hood-quorum-reads-and-mutating-state/) | Blog, part 3 | How reads avoid quorum cost and how state changes are handled. |
| 6 | [Reducing costs using quorum sets](https://aws.amazon.com/blogs/database/amazon-aurora-under-the-hood-reducing-costs-using-quorum-sets/) | Blog, part 4 | Mixes full and partial copies to lower cost without losing durability. |
| 7 | [Architecture decisions in Neon](https://neon.com/blog/architecture-decisions-in-neon) and [Deep dive into Neon storage engine](https://neon.com/blog/get-page-at-lsn) | Blog | The same idea applied to PostgreSQL. It shows the WAL service and the "get page at LSN" request. Compare with the WAL chapters of PostgreSQL Internals. |
| 8 | [Aurora DSQL: Serverless, Scalable, Global OLTP](https://db.cs.cmu.edu/events/pg-vs-world-aurora-dsql-marc-brooker/) (Marc Brooker, [video](https://www.youtube.com/watch?v=mK2h9VPRgnI)) | Talk | The next step: a PostgreSQL-compatible system with no single writer. |
| 9 | [DSQL Vignette: Aurora DSQL, and A Personal Story](https://brooker.co.za/blog/2024/12/03/aurora-dsql.html), then [Reads and Compute](https://brooker.co.za/blog/2024/12/04/inside-dsql.html) | Blog | The designer explains the reasoning behind the design. |
| 10 | [HorizonDB: Co-Designing PostgreSQL and Azure for Cloud-Native OLTP](https://db.cs.cmu.edu/events/pg-vs-world-horizondb-adam-prout/) | Talk | A second view of the same architecture on a different cloud. |

### Theme B. Distributed transactions and global consistency

| Read | Kind | Why it matters |
| --- | --- | --- |
| [Dynamo: Amazon's Highly Available Key-value Store](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) (2007) | Paper | The start of the NoSQL wave. It gives up consistency for availability. |
| [Bigtable: A Distributed Storage System for Structured Data](https://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf) (2006) | Paper | The wide-column model on top of a log-structured store. |
| [Large-scale Incremental Processing Using Distributed Transactions and Notifications](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/36726.pdf) (Percolator, 2010) | Paper | Snapshot transactions built on top of Bigtable. Many later systems copy this design. |
| [Spanner: Google's Globally-Distributed Database](https://static.googleusercontent.com/media/research.google.com/en//archive/spanner-osdi2012.pdf) (2012) | Paper | TrueTime bounds clock error. This gives external consistency across the globe. |
| [Calvin: Fast Distributed Transactions for Partitioned Database Systems](http://cs.yale.edu/homes/thomson/publications/calvin-sigmod12.pdf) (2012) | Paper | The opposite approach: agree on the transaction order first, then run deterministically. |
| [F1: A Distributed SQL Database That Scales](https://research.google/pubs/f1-a-distributed-sql-database-that-scales/) (2013) | Paper page | Shows how SQL was put on top of Spanner for Google's ads system. |
| [Living without atomic clocks: Where CockroachDB and Spanner diverge](https://www.cockroachlabs.com/blog/living-without-atomic-clocks/) | Blog | How a system with no special clock hardware gets similar guarantees. |
| [FoundationDB: A Distributed Unbundled Transactional Key Value Store](https://www.foundationdb.org/files/fdb-paper.pdf) (2021) | Paper | A small, strict transaction core with testing built in from the start. |
| [In Search of an Understandable Consensus Algorithm (Raft)](https://raft.github.io/raft.pdf) (2014) | Paper | The consensus algorithm most new systems use. |
| [Millions of Tiny Databases](https://www.usenix.org/system/files/nsdi20-paper-brooker.pdf) (NSDI 2020) | Paper | How a cloud service manages a huge fleet of small replicated state machines. |
| [Eventually Consistent](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Werner Vogels) | Blog | The plain-language case for the consistency models that Dynamo used. |
| [Jepsen consistency map](https://jepsen.io/consistency) and [Jepsen analyses](https://jepsen.io/analyses) | Reference | Independent tests that show what each database really guarantees. Read a few reports. |

### Theme C. Analytics, warehouses, and lakehouses

Dremel, Snowflake, Photon, DuckDB, Yellowbrick, and Redshift are in the [advanced course](advanced-15-721.md).
Read these additional works.

| Read | Kind | Why it matters |
| --- | --- | --- |
| [ClickHouse - Lightning Fast Analytics for Everyone](https://www.vldb.org/pvldb/vol17/p3731-schulze.pdf) (VLDB 2024) | Paper | A clear tour of MergeTree storage, skipping indexes, and the query engine. |
| [ClickHouse architecture overview](https://clickhouse.com/docs/academic_overview) | Docs | A companion to the paper. |
| [Chasing that 32 ms tail latency in ClickHouse](https://clickhouse.com/blog/chasing-32ms-tail-latency-clickhouse) | Blog | A real performance investigation across the whole stack. |
| [Napa: Powering Scalable Data Warehousing with Robust Query Performance at Google](https://www.vldb.org/pvldb/vol14/p2986-sankaranarayanan.pdf) (VLDB 2021) | Paper | Trades data freshness against query speed in a controlled way. |
| [Delta Lake: High-Performance ACID Table Storage over Cloud Object Stores](https://www.vldb.org/pvldb/vol13/p3411-armbrust.pdf) (VLDB 2020) | Paper | A transaction log over plain files in object storage. |
| [DuckLake: SQL as a Lakehouse Format](https://duckdb.org/2025/05/27/ducklake.html) | Blog | Argues that table metadata belongs in a SQL database, not in files. |
| [Introducing Velox](https://engineering.fb.com/2023/03/09/open-source/velox-open-source-execution-engine/) | Blog | Why one shared execution engine can serve many query systems. |
| [Ursa: A Lakehouse-Native Data Streaming Engine for Kafka](https://www.vldb.org/pvldb/vol18/p5184-guo.pdf) (VLDB 2025) | Paper | Streams and tables meet in the same storage layer. |

### Theme D. Storage engines and in-memory systems

| Read | Kind | Why it matters |
| --- | --- | --- |
| [The Log-Structured Merge-Tree (LSM-Tree)](https://www.cs.umb.edu/~poneil/lsmtree.pdf) (O'Neil et al., 1996) | Paper | The base idea of RocksDB, Cassandra, and many other stores. |
| [RocksDB overview](https://github.com/facebook/rocksdb/wiki/RocksDB-Overview) and the [RocksDB blog](https://www.rocksdb.org/blog/) | Docs and blog | How a widely used LSM engine is organized and tuned. |
| [Fast Serializable Multi-Version Concurrency Control for Main-Memory Database Systems](https://db.in.tum.de/~muehlbau/papers/mvcc.pdf) (HyPer, SIGMOD 2015) | Paper | Compact MVCC that fast analytics scans can read. |
| [LeanStore: In-Memory Data Management Beyond Main Memory](https://db.in.tum.de/~leis/papers/leanstore.pdf) (ICDE 2018) | Paper | A buffer manager designed for fast SSDs and many cores. |
| [Umbra: A Disk-Based System with In-Memory Performance](https://db.in.tum.de/~freitag/papers/p29-neumann-cidr20.pdf) (CIDR 2020) | Paper | Compiles queries and keeps data on disk without losing in-memory speed. |
| [B-trees and database indexes](https://planetscale.com/blog/btrees-and-database-indexes) and [IO devices and latency](https://planetscale.com/blog/io-devices-and-latency) | Blog | Clear, visual explanations from a team that runs MySQL at scale. |
| [The Part of PostgreSQL We Hate the Most](https://www.cs.cmu.edu/~pavlo/blog/2023/04/the-part-of-postgresql-we-hate-the-most.html) (Pavlo) | Blog | An honest review of PostgreSQL MVCC and vacuum design. Pair it with the MVCC chapters you have read. |

### Theme E. Search, vector, and object-storage-native systems

| Read | Kind | Why it matters |
| --- | --- | --- |
| [Efficient and robust approximate nearest neighbor search using HNSW graphs](https://arxiv.org/pdf/1603.09320) | Paper | The graph index behind most vector search. |
| [turbopuffer: fast search on object storage](https://turbopuffer.com/blog/turbopuffer) and the [architecture page](https://turbopuffer.com/docs/architecture) | Blog and docs | A database built directly on object storage, with a cache for hot data. |
| [How to build a distributed queue in a single JSON file on object storage](https://turbopuffer.com/blog/object-storage-queue) | Blog | A small, concrete lesson in building on object storage with compare-and-swap. |
| [TopK: Billion-Scale Hybrid Retrieval from the Ground Up](https://db.cs.cmu.edu/events/pg-vs-world-topk-marek-galovic/) | Talk | Combines keyword and vector retrieval in one engine. |

### Theme F. Scaling PostgreSQL and MySQL in production

These posts show how real companies kept a single-node database going as they grew.

| Read | Why it matters |
| --- | --- |
| [How Figma's Databases Team Lived to Tell the Scale](https://www.figma.com/blog/how-figmas-databases-team-lived-to-tell-the-scale/) | Moving from one PostgreSQL instance to horizontal sharding. |
| [Herding elephants: Lessons learned from sharding Postgres at Notion](https://www.notion.com/blog/sharding-postgres-at-notion) | A sharding plan with a careful migration. |
| [How Discord Stores Trillions of Messages](https://discord.com/blog/how-discord-stores-trillions-of-messages) | A move from Cassandra to ScyllaDB, with the reasons. |
| [The slotted counter pattern](https://planetscale.com/blog/the-slotted-counter-pattern) | A fix for a hot-row problem in MySQL. It also applies to PostgreSQL. |

### Theme G. Query execution and optimization

| Read | Kind | Why it matters |
| --- | --- | --- |
| [Everything You Always Wanted to Know About Compiled and Vectorized Queries But Were Afraid to Ask](https://www.vldb.org/pvldb/vol11/p2209-kersten.pdf) (VLDB 2018) | Paper | A fair comparison of the two main execution designs. |
| [How Good Are Query Optimizers, Really?](https://www.vldb.org/pvldb/vol9/p204-leis.pdf) (VLDB 2015) | Paper | Shows that cardinality estimation errors, not cost models, cause most bad plans. |
| [Velox: Meta's Unified Execution Engine](https://www.vldb.org/pvldb/vol15/p3372-pedreira.pdf) (VLDB 2022) | Paper | One reusable execution library under several query systems. |

### Theme H. Streaming and dataflow

| Read | Kind | Why it matters |
| --- | --- | --- |
| [The Dataflow Model](https://www.vldb.org/pvldb/vol8/p1792-Akidau.pdf) (VLDB 2015) | Paper | The model behind event-time windows and late data handling in modern stream systems. |
| [Ursa](https://www.vldb.org/pvldb/vol18/p5184-guo.pdf) (VLDB 2025) | Paper | A recent system that joins streaming and lakehouse storage. |

### Theme I. Learned, self-driving, and AI-era databases

| Read | Kind | Why it matters |
| --- | --- | --- |
| [The Case for Learned Index Structures](https://arxiv.org/pdf/1712.01208) (Kraska et al., 2017) | Paper | Started the work on replacing index structures with models. |
| [Self-Driving Database Management Systems](https://www.cidrdb.org/cidr2017/papers/p42-pavlo-cidr17.pdf) (Pavlo et al., CIDR 2017) | Paper | A vision of a database that tunes itself. |
| [What Goes Around Comes Around... And Around...](https://db.cs.cmu.edu/papers/2024/whatgoesaround-sigmodrec2024.pdf) (Stonebraker and Pavlo, 2024) | Paper | A 20-year review of data models and query languages. Useful context for SQL, graphs, and vectors. |

### Theme J. Lakehouse and data platform design

| Read | Kind | Why it matters |
| --- | --- | --- |
| [Lakehouse: A New Generation of Open Platforms that Unify Data Warehousing and Advanced Analytics](https://www.cidrdb.org/cidr2021/papers/cidr2021_paper17.pdf) (CIDR 2021) | Paper | The founding argument for the lakehouse design. |
| [Shared Foundations: Modernizing Meta's Data Lakehouse](https://www.cidrdb.org/cidr2023/papers/p77-chattopadhyay.pdf) (CIDR 2023) | Paper | How a very large company moved to shared lakehouse components. |
| [Apache Iceberg table spec](https://iceberg.apache.org/spec/) | Spec | A concrete table format. Read it next to the Delta Lake paper. |

## Part 4. Systems with unusual designs

These are systems where a team made a bold choice. Read one to widen your view of what a database can be.

### TigerBeetle: a database built for one job, with strict engineering

TigerBeetle is an OLTP database for financial transactions. It is useful to study for its
engineering method as much as for its design. Suggested order:

1. [A Database Without Dynamic Memory Allocation](https://tigerbeetle.com/blog/2022-10-12-a-database-without-dynamic-memory): all memory is set at startup.
2. [Rediscovering Transaction Processing From History and First Principles](https://tigerbeetle.com/blog/2024-07-23-rediscovering-transaction-processing-from-history-and-first-principles): why a ledger needs a different design.
3. [Tiger Style](https://github.com/tigerbeetle/tigerbeetle/blob/main/docs/TIGER_STYLE.md): the team's coding rules.
4. [Fuzzer Blind Spots (Meet Jepsen!)](https://tigerbeetle.com/blog/2025-06-06-fuzzer-blind-spots-meet-jepsen) and the [Jepsen report on TigerBeetle 0.16.11](https://jepsen.io/analyses/tigerbeetle-0.16.11): how deterministic simulation testing met an outside test team.
5. [Mathematics of Consensus](https://tigerbeetle.com/blog/2025-11-22-mathematics-of-consensus): a lecture-style post on consensus.
6. [A Trillion Transactions](https://tigerbeetle.com/blog/2026-03-19-a-trillion-transactions): what happens at very large scale.
7. Browse the full [TigerBeetle blog](https://tigerbeetle.com/blog/) for more, such as time handling and fuzzing.

### Other systems to study

| System | Read | What is unusual |
| --- | --- | --- |
| ClickHouse | The three ClickHouse items in Theme C | Very fast columnar storage, built as an open engine with a strong performance culture. See also the [ClickHouse blog](https://clickhouse.com/blog). |
| SQLite | [Atomic Commit In SQLite](https://www.sqlite.org/atomiccommit.html) | A full ACID database in one library and one file. |
| Materialize | [Materialize blog](https://materialize.com/blog/) | Keeps SQL views up to date as data changes. It builds on [differential dataflow](https://github.com/TimelyDataflow/differential-dataflow). |
| SpacetimeDB | [Inverting the Backend](https://db.cs.cmu.edu/events/pg-vs-world-spacetimedb-tyler-cloutier/) | A game server that runs inside the database. |
| Sirius | [A GPU-Native SQL Engine](https://db.cs.cmu.edu/events/pg-vs-world-sirius-a-gpu-native-sql-engine-xiangyao-yu/) | Runs whole SQL queries on the GPU. |
| TonicDB | [Databases without an OS?](https://db.cs.cmu.edu/events/pg-vs-world-tonicdb-filip-obradovic/) | Rethinks the database and the operating system together. |
| Multigres | [Horizontal scaling for PostgreSQL](https://db.cs.cmu.edu/events/pg-vs-world-multigres-sugu-sougoumarane/) | The Vitess approach, now for PostgreSQL. |

## Part 5. Andy Pavlo's seminar series

Each series groups many systems around one theme. The talks come from the engineers who built the systems.
Pick the series that matches your current interest. Each series page has a YouTube link for every talk.

| Series | Theme | Talks you may want first |
| --- | --- | --- |
| [PostgreSQL vs. The World (Spring 2026)](https://db.cs.cmu.edu/seminars/spring2026/) | Systems built on or around PostgreSQL | Aurora DSQL, HorizonDB, YugabyteDB, Multigres, TopK, turbopuffer, Redpanda Oxla ([playlist](https://www.youtube.com/playlist?list=PLSE8ODhjZXjZEVnVTtgDWw6P3wA_gDwj4)) |
| [Future Data Systems (Fall 2025)](https://db.cs.cmu.edu/seminars/fall2025/) | Lakehouse formats, catalogs, and storage | Apache Iceberg planning, Apache Hudi, DuckLake, Vortex, ADBC, Databricks multi-statement transactions, XTDB, Apache Polaris, Apache Fluss |
| [SQL or Death? (Spring 2025)](https://db.cs.cmu.edu/seminars/spring2025/) | Query languages and optimizers | Pipe syntax in SQL, PRQL, Malloy, StarRocks optimizer, Apache Pinot optimizer, MariaDB optimizer |
| [Database Building Blocks (Fall 2024)](https://db.cs.cmu.edu/seminars/fall2024/) | Reusable components | DataFusion, DataFusion Comet, InfluxDB 3.0 (FDAP stack), OpenDAL, ParadeDB |
| [Whatever Andy Found Interesting While Out At Sea (Fall 2026)](https://db.cs.cmu.edu/seminars/fall2026/) | Andy's own picks | Check the page for the current lineup |

The [seminar index](https://db.cs.cmu.edu/seminars/spring2026/) also lists older series: ML⇄DB (2023), ¡Databases! (2022),
the Vaccination Database Talks (2021-2022), Time Series Database Lectures (2017), and Hardware Accelerated Database Lectures (2018).

## Part 6. Blogs and people to follow

Read these over time. They are the best sources of practical detail.

| Source | Why follow it |
| --- | --- |
| [TigerBeetle blog](https://tigerbeetle.com/blog/) | Testing, consensus, and careful systems engineering. |
| [ClickHouse blog](https://clickhouse.com/blog) | Performance work and columnar internals. |
| [Marc Brooker's blog](https://brooker.co.za/blog/) | Clear essays on distributed systems from an AWS database engineer. |
| [Neon blog](https://neon.com/blog) | Storage and compute separation for PostgreSQL. |
| [PlanetScale blog](https://planetscale.com/blog) | MySQL, Vitess, and storage fundamentals. |
| [turbopuffer blog](https://turbopuffer.com/blog) | Object-storage-native search. |
| [DuckDB blog](https://duckdb.org/news/) | Embedded analytics and file format work. |
| [Materialize blog](https://materialize.com/blog/) | Incremental view maintenance and streaming SQL. |
| [Jepsen analyses](https://jepsen.io/analyses) | Independent consistency tests of real systems. |
| [Andy Pavlo's blog](https://www.cs.cmu.edu/~pavlo/blog/) | Yearly reviews and strong opinions on database trends. |
| [Aphyr](https://aphyr.com/) | The author of Jepsen. Deep distributed systems writing. |
| [The Morning Paper archive](https://blog.acolyer.org/) | Short summaries of many systems papers. It is an archive, not an active blog. |
| [The Paper Trail](https://www.the-paper-trail.org/) | Distributed systems and database explanations. |
| [DoltHub blog](https://www.dolthub.com/blog/) | Version-controlled databases and storage engine details. |
| [ScyllaDB blog](https://www.scylladb.com/2025/) | Shard-per-core design and I/O scheduling. |

## Suggested order for a first pass

If you want a path through all of this, use this order. It takes about 10-12 sessions of 60-90 minutes each.

1. Part 3, Theme A (Aurora, Neon, DSQL). This is the biggest change in OLTP.
2. Part 3, Theme B (Spanner, Percolator, Calvin, FoundationDB).
3. Part 4, TigerBeetle posts 1-4.
4. Part 3, Theme C (ClickHouse, Delta Lake, DuckLake).
5. Part 3, Theme D (LSM tree, HyPer, LeanStore, the PostgreSQL critique).
6. Part 2, the Systems Award entries that you did not yet read.
7. Part 3, Theme F (Figma, Notion, Discord).
8. Part 5, one seminar series of your choice.
9. Part 3, Theme E and the remaining Part 4 systems.

## Notes on gaps

- Some papers on the award pages have no free PDF link on those pages. The table says "search the title"
  for those. They were not linked here because a working link could not be confirmed.
- Not all award-winning work fits a database fundamentals path. The picks above favor work that changed
  system design.
- Update this page when new award lists appear. The SIGMOD and VLDB pages above are the sources.
