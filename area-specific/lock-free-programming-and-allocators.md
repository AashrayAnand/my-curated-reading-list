# Lock-Free Programming & Memory Allocators

## Primers (start here)

1. **An Introduction to Lock-Free Programming** — Jeff Preshing (2012)
   https://preshing.com/20120612/an-introduction-to-lock-free-programming/

2. **Lock-Free Data Structures: Stacks** — Jeff Preshing (2013)
   https://preshing.com/20130930/double-checked-locking-is-fixed-in-cpp11/
   (See also his full lock-free series at https://preshing.com/archives/)

3. **What Every Systems Programmer Should Know About Concurrency** — Matt Kline (2020)
   https://assets.bitbashing.io/papers/concurrency-primer.pdf

4. **Crossbeam epoch internals** — Aaron Turon / Stjepan Glavina
   https://github.com/crossbeam-rs/crossbeam (source + design docs in repo)

## Core Papers

5. **mimalloc: Free List Sharding in Action** — Daan Leijen, Benjamin Zorn, Leonardo de Moura (2019), Microsoft Research
   https://www.microsoft.com/en-us/research/publication/mimalloc-free-list-sharding-in-action/

6. **The Art of Multiprocessor Programming** — Maurice Herlihy & Nir Shavit (2008, revised 2012)
   Chapters 10–11: lock-free stacks, pools, ABA problem.
   https://booksite.elsevier.com/9780123973375/

7. **Hazard Pointers: Safe Memory Reclamation for Lock-Free Objects** — Maged Michael (2004), IEEE TPDS
   https://ieeexplore.ieee.org/document/1291819

## Deeper Reading

8. **The Slab Allocator: An Object-Caching Kernel Memory Allocator** — Jeff Bonwick (1994), USENIX
   https://www.usenix.org/legacy/publications/library/proceedings/bos94/bonwick.html

9. **Scalable Lock-Free Dynamic Memory Allocation** — Maged Michael (2004), PLDI
   https://dl.acm.org/doi/10.1145/996841.996848

10. **Systems Programming: Coping with Parallelism** — R.K. Treiber (1986), IBM Technical Report
    https://dominoweb.draco.res.ibm.com/58319a2ed2b1078985257003004617ef.html

11. **Practical Lock-Freedom** — Keir Fraser (2004), Cambridge PhD thesis
    Chapter 5: epoch-based reclamation.
    https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-579.pdf

12. **Non-Blocking Steal-Half Work Queues** — Danny Hendler & Nir Shavit (2002)
    https://dl.acm.org/doi/10.1145/571825.571876
