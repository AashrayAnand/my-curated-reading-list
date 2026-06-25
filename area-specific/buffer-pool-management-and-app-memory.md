# Buffer Pool Management & Application Memory in Databases

## Database Buffer Pool Design (directly relevant)

1. **"The Design of the PostgreSQL Buffer Manager"** — PostgreSQL source docs
   https://www.postgresql.org/docs/current/storage-page-layout.html
   (Also: `src/backend/storage/buffer/README` in the PG source tree)
   Covers pin/unpin, clock sweep, shared_buffers, buffer descriptors.

2. **"Buffer Pool Management in Database Systems"** — Goetz Graefe (2023)
   https://dl.acm.org/doi/10.1145/3588921
   Comprehensive survey of buffer pool designs across systems. Covers pinning, eviction policies, and memory-mapped alternatives.

3. **"Are You Sure You Want to Use MMAP in Your Database Management System?"** — Crotty et al. (2022)
   https://db.cs.cmu.edu/papers/2022/cidr2022-p13-crotty.pdf
   Why mmap for I/O is problematic but mmap for buffer pool backing (our use) is different. Relevant context for understanding the design space.

4. **"LeanStore: In-Memory Data Management Beyond Main Memory"** — Leis et al. (2018)
   https://db.in.tum.de/~leis/papers/leanstore.pdf
   Modern buffer pool design with pointer swizzling. Shows how modern systems avoid per-page allocation overhead.

5. **"Anti-Caching: A New Approach to Database Management System Architecture"** — DeBrabant et al. (2013)
   https://www.vldb.org/pvldb/vol6/p1942-debrabant.pdf
   Cold/hot page management relevant to eviction under memory pressure.

## Memory Allocator Fragmentation (the problem we're solving)

6. **"mimalloc: Free List Sharding in Action"** — Leijen et al. (2019), Microsoft Research
   https://www.microsoft.com/en-us/research/publication/mimalloc-free-list-sharding-in-action/
   The allocator we're fighting against. Explains thread-local segments, segment abandonment.

7. **"Understanding glibc malloc"** — Azeria Labs (blog)
   https://azeria-labs.com/heap-exploitation-part-1-understanding-the-glibc-heap-implementation/
   General heap allocator internals — useful for understanding why app-managed memory avoids fragmentation.

## Unsafe Rust for Systems Programming

8. **"Learn Rust With Entirely Too Many Linked Lists"** — Alexis Beingessner
   https://rust-unofficial.github.io/too-many-lists/
   Covers why data structures with shared ownership require unsafe in Rust, and how to do it safely.

9. **"The Rustonomicon: The Dark Arts of Unsafe Rust"** — official
   https://doc.rust-lang.org/nomicon/
   Chapters on raw pointers, Send/Sync, atomics, and when unsafe is justified.

10. **"Rust Atomics and Locks"** — Mara Bos (2023, O'Reilly)
    https://marabos.nl/atomics/
    Practical guide to atomic operations and lock-free programming in Rust. Directly relevant to our pin_count atomics and memory ordering.

11. **"Writing Unsafe Rust: Correctly and Safely"** — Jon Gjengset (blog/YouTube)
    https://www.youtube.com/watch?v=QAz-maaH0KM
    Practical examples of sound unsafe Rust abstractions with interior mutability.

## Application-Managed Memory Regions

12. **"Slab Allocation"** — Jeff Bonwick (1994), USENIX
    https://www.usenix.org/legacy/publications/library/proceedings/bos94/bonwick.html
    Original kernel slab allocator paper. Our frame region is conceptually a single-size slab.

13. **"Memory Management in Apache Arrow"** — Apache docs
    https://arrow.apache.org/docs/format/Columnar.html#buffer-alignment-and-padding
    Shows how modern data systems use pre-allocated memory pools with manual lifecycle management.

14. **"Umbra: A Disk-Based System with In-Memory Performance"** — Neumann & Freitag (2020)
    https://db.in.tum.de/~freitag/papers/p29-neumann-cidr20.pdf
    Uses mmap-backed buffer pool with application-level page management — very similar to our approach.

## Pin-Based Concurrency Control

15. **"Optimistic Lock Coupling"** — Leis et al. (2019)
    https://db.in.tum.de/~leis/papers/olc.pdf
    Not directly about buffer pinning, but covers how modern DB systems coordinate concurrent access to shared pages without traditional locking.

16. **"The ART of Practical Synchronization"** — Leis et al. (2016)
    https://db.in.tum.de/~leis/papers/artsync.pdf
    Epoch-based memory reclamation as an alternative to pinning — worth understanding the tradeoff space.
