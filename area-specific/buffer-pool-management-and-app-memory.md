# Buffer Pool Management & Application Memory in Databases

1. **"Are You Sure You Want to Use MMAP in Your Database Management System?"** — Crotty et al. (2022)
   https://db.cs.cmu.edu/papers/2022/cidr2022-p13-crotty.pdf
   Why mmap for I/O is problematic but mmap for buffer pool backing (our use) is different.

2. **"Anti-Caching: A New Approach to Database Management System Architecture"** — DeBrabant et al. (2013)
   https://www.vldb.org/pvldb/vol6/p1942-debrabant.pdf
   Cold/hot page management relevant to eviction under memory pressure.

3. **"Slab Allocation"** — Jeff Bonwick (1994), USENIX
   https://www.usenix.org/legacy/publications/library/proceedings/bos94/bonwick.html
   Original kernel slab allocator paper. Our frame region is conceptually a single-size slab.

4. **"Umbra: A Disk-Based System with In-Memory Performance"** — Neumann & Freitag (2020)
   https://db.in.tum.de/~freitag/papers/p29-neumann-cidr20.pdf
   Uses mmap-backed buffer pool with application-level page management — very similar to our approach.

5. **"Lock-freedom without garbage collection"** — Aaron Turon (2015)
   https://aturon.github.io/blog/2015/08/27/epoch/#epoch-based-reclamation
   Epoch-based reclamation for lock-free data structures in Rust. Crossbeam crate design.
