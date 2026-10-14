# My Curated Reading List

A weekly curated reading list for growing technical depth in **databases**, **AI/LLMs**, and **systems design** — with a philosophical perspective sprinkled in.

## How It Works

Each week contains a curated list of resources fitting a ~180 min/week budget (45 min/day × 4 days):

- 📄 **1 Anchor Paper** — a significant database paper, historical or cutting edge
- 🤖 **1 AI Deep Dive** — building understanding of LLMs and modern AI
- 💻 **2–3 Tech Blogs** — databases, distributed systems, engineering practices
- 🌿 **1 Perspective Read** — philosophical, introspective, or engineering culture

Check off items as you read them. Add notes and reflections at the bottom of each week.

## Structure

```
weeks/             — Weekly reading lists (week-01.md, week-02.md, ...)
area-specific/     — Deep, ground-up reading paths on a single topic (e.g. disk I/O + async runtimes)
database-expertise/ — Watch-and-read database track: CMU 15-445 and 15-721 lectures paired with
                     PostgreSQL Internals chapters and papers or blogs
inference-engineering/ — Book-aligned AI inference path: GPU execution, KV memory, serving,
                         distributed systems, production, benchmarks, and modalities
resources/         — User-supplied reference material
systems-knowledge/ — The fundamentals bank: a ~1-year syllabus of timeless systems knowledge + a
                     plan for turning it into measurable capability
systems-from-rust/ — Learning systems concepts through Rust, then stripping the abstraction to the
                     machine underneath (starts with async/futures internals)
context/           — Reading profile, decision log, and backlog for curation continuity
.github/           — Copilot instructions for the reading buddy agent
```

## Deep-Dive Sections

Beyond the weekly cadence, these are standalone, tiered reading paths for going deep on one area:

- [Systems Knowledge](systems-knowledge/README.md) — the fundamentals every systems programmer should
  internalize (memory & CPU architecture, memory models, concurrency, lock-free, measurement), plus a
  1-year plan for converting reading into demonstrable skill.
- [Database Expertise: Intro](database-expertise/intro-15-445.md) — CMU 15-445 lectures, two per week,
  with PostgreSQL Internals chapters and a paper or blog each week. No coding projects.
- [Database Expertise: Advanced](database-expertise/advanced-15-721.md) — CMU 15-721 lectures with the
  same weekly format.
- [Database Expertise: Industry SOTA](database-expertise/industry-sota.md) — a who's who of seminal
  database papers, award winners, blogs, and talks, grouped by area of innovation.
- [Systems From Rust](systems-from-rust/README.md) — systems concepts taught through Rust and traced
  down to the machine. First entry: what a future really is (async/futures internals).
- [Inference Engineering](inference-engineering/README.md) — a dedicated path through the Baseten
  book and twelve-stage AI infrastructure roadmap, connected to database execution, memory
  management, scheduling, and measurement; includes optional hardware-scaled labs.
- [Area-Specific](area-specific/) — focused ground-up paths (disk I/O and async runtimes,
  buffer-pool management, lock-free programming and allocators, and
  [agentic development and verification harnesses](area-specific/agentic-development-and-verification-harnesses.md)).
- [Concurrency at Scale](area-specific/concurrency-at-scale.md): synchronization amortization,
  batching, backpressure, logging scalability, and per-core ownership.

## Current Progress

- [Week 1](weeks/week-01.md) — Distributed Storage & The Transformer Revolution
- [Week 2](weeks/week-02.md) — Global Consistency & AI Fundamentals Pivot
- [Backlog](context/backlog.md) — Readings deferred from their target week
