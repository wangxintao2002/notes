# Introduction
- Memory consistency defines the behavior of reads and writes to different locations(as observed by other processors). It deals with when will writes to X propogate to other processors, relative to reads and writes to other address. It defines the allowed behavior of loads and stores to different addresses in a parallel system.
- For performance, multiprocessors reorder memory operations in strange ways.

# Memory operation ordering
- Four types of memory operation orderings:
    - W->R: write to X must commit before subsequent read from Y
    - R->R: read from X must commit before subsequent read from Y
    - R->W: read from X must commit before subsequent write to Y
    - W->W: write to X must commit before subsequent write to Y
- A sequential consistent memory system maintains all four memory operaiton orderings.

# Sequential consistency
- The result of any parallel execution is the same if all the memory operations were executed in some sequential order, and the memory operation of any one processor are executed in program order.

# Relaxing memory operation ordering
- A relaxed memory model may allow violation of four orderings. 
- Motivation for relaxed consistency: hiding latency.
    - overlap memory access opretions with other operations when they are independent. E.g. using a write buffer that relaxes W->R ordering and allows reads to proceed in front of prior writes.

# Allowing reads to move ahead of writes
- Total store ordering(TSO):
    - Processor P can read B before its write to A is seen by ALL processors, reads by other processors cannot return new value of A until the write of A is observed by ALL processors.
- Processor consistency(PC):
    - Any processor can read new value of A before the write is observed by all processors.
- In TSO and PC, only W->R ordering is relaxed.
- Partial store ordering(PSO):
    - Relax W->R and W->W.
- Weak ordering(WO), Release consistency(RC)
    - relax all four reorderings.
    - processors support special sync operations(MEMORY FENCE) when neccesary.

# Why reordering
- W->W: processor might reorder write operations in a write buffer (e.g. one is a cache miss and the other is a hit).
- R->W, R->R: processor might reorder independent instructions in an instruction stream(out-of-order execution).

# Acquire/release semantics
- Operation X with acquire semantics: prevent reordering of X with any load/storee after X in program order.
    - example: taking a lock must have acquire semantics.
- Operation X with release semantics: prevent reordering of X with any load.store before X in program order.
    - example: releasing a lock must have release semantics.

