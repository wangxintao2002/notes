# Introduction
- The cache coherence problem: modern processors replicate contentss of memory in local caches, processors can observe different values for the same memory location.
- Intuitive expectation of shared memory: reading value at address X should return the last value written to address X by any processor.
- Coherence problem can happen in a single CPU system: consider I/O device performing DMA data transfer. CPU may not have flush cache to memory yet before the device sending data; When DMA data transfer finishs, CPU may read stale data that were not yet be load in cache.
    - Common solutions: CPU writes to buffers using uncached stores, or OS marks buffers as not-cachable, or Explicitly flush pages from cache when IO completes.
- The definition of coherence: a memory system is coherent if:
    - A read by a processor P that follows a write by P, with no other writes by another processors always return the value written by P.
    - A read by a processor P that follows a write by another processor returns the written value if the read and write are sufficiently seperated in time and no other writes between two access.(need to deal with cache).
    - Two writes to the same location by any two processors are seen in the same order of all processors(serialized writes).

# Snooping cache-coherence schemes
- Main idea: all coherence-related activity is broadcast to all processors' cache controller. Cache controllers monitor memory operations and react accordingly to maintain memory coherence.
## Cache coherence with write-back caches
- MSI write-back invalidation protocol: Each cache line has three states, Modified, Shared and Invalid; A line in a modified state can be modified without notifying other caches. When cache controller snoops a request for exclusive access to line it contains, it must invalidate the line in its own cache.
    - Two processor operations: read, write.
    - Three coherence-related bus transactions: BusRd, BusRdX(intent to modify), flush(write to memory).
- MESI write-back invalidation protocol: Add a Exclusive clean state. When read a cache line, if no other caches asserts shared on this line, then move to Exclusive clean state.
    - MSI protocol needs two transactions for the common case of reading a address and then writing to it(BusRd and BusRdX). With MESI protocol, upgrade from E to M does not need a transaction.
## Unintended communication via false sharing
- Instead of using:
    // allocate per-thread variable for local per-thread accumulation
    int myPerThreadCounter[NUM_THREADS];
    use:
    // allocate per thread variable for local accumultion
    struct PerThreadState {
        int myPerThreadCounter;
        char padding[CACHE_LINE_SIZE - sizeof(int)];
    }
    perThreadState myPerThreadCounter[NUM_THREADs];
    to avoid false sharing.
## Multi-level cache hierarchies
- Changes made to data at higher level cache may not be visible to the lower level cache controller. Maintain "inclusion" to make snooping work.
    - Inclusion property: All lines in higher cache are also in lower cache. If line is in M state in L1, it must also be in M state in L2.
## Implementing snooping
- One outstanding memory request per processor, cache can stall processor as it is carrying out coherence operations, system interconnect is an atomic shared bus.(one cache communication at a time)
### Transaction on an atomic bus
- Client granted bus access -> Client places command on bus -> Response to command by another bus client placed on bus -> Next client obtains bus access
### Cache miss logic on a uniprocessor
- Detemin cache set -> Check cache tag(assume miss) -> Assert request for access to bus -> Wait for bus grant -> Send address + command on bus -> Receive data on bus.
### Multi-processor cache controller behavior
- Duplicate tags to avoid contention between local processor's and remote processor's(from the bus) tag checking routine. Tags must stay in sync, so tag update by one will still block the other, but modifying tags is infrequent.
### Handling write backs
- Write backs involve two bus transactions: Incoming line(from processor), outgoing line(to the memory).
- Use write back buffer to provide asynchronous write to memory.
## Problem
- Snooping-based protocol cannot be scaled in large machines due to the broadcast operation. 

# Directory-based cache coherence
- Idea: avoid broadcast by storing the status of the line in one place: a "directory".
    - The directory entry for a cache line contains information about the state of the cache line in all caches.
    - May use distributed directories, each processor has its own directory.
    - transfering data only involves point2point communication.

