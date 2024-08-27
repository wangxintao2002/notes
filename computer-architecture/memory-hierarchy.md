# Introduction
- Latency: The time delay between a request for data and the beginning of the data transfer.
- Bandwidth:  The amount of data that can be transferred per unit of time.
- The lack of significant performance of single core processors has led to slowdown in the growth of the performance gap between processors and DRAM.
- Multicore processors need more memory bandwidth.
    - [case study]: i7-6700 can generate 2 data memory references per clock cycle with 4 cores and a 4.2 GHz clock rate. The i7 can generate a peak of 32.8 million 64-bit data memory references per second, that goes to 409.6 GiB/s.

# Processor Performance
- CPU execution time = (CPU clock cycles + Memory stall cycles) x Clock cycle time
    - Assumes CPU clock cycles include the time to handle cache hit.
- Memory stall cycles = Number of misses x Miss penalty = IC x Misses / Instructions x Miss penalty = IC x Memory accesses / Instructions x Miss rate x Miss penalty

# Cache Performance
- Mesurement of cache performance: Average memory access time(AMAT).
    - AMAT = hit time + miss rate * Miss penalty

## AMAT and Processor performance
- AMAT due to cache misses predicts processor performance?
    - Assume all memory store due to cache misses.
    - For in-order execution processor, yes. For out-of-order execution processor, no.
- CPU time = IC x (CPI execution + Memory stall clock cycles / Instruction) x Clock cycle time
- Cache behavior can have enormous impact on performance.

# Cache Optimizations
- 3C's:
    - Compulsory miss: The very first access to a block cannot be in the cache.
        - Increase block size -> higher miss penalty, Increase block size -> fewer blocks -> conflict miss. 
    - Capacity miss: The cache cannot contain all the blocks needed.
        - Enlarge cache size -> longer hit time and higher cost and power. 
    - Confict miss: Too many blocks map to its set.
        - Increase associativity -> more hardware -> decrese clock rate -> lower performance.

## Larger Block Size to Reduce Miss Rate
- Larger blocks take advantage of spatial locality. But larger blocks means increased miss penalty and fewer blocks that would bring conflict miss. The increase in miss penalty brought by large block may outweight the decrease in miss rate.
- The selection of block size depends on the latency and the bandwidth of the lower-level memory.
    - High latency and high bandwidth encourage larger block size because more bytes per miss for a small increase in miss penalty.
    - Low latency and low bandwidth encourage smaller block size because there is no reason to transfer a large block slowly.

## High Associativity to Reduce Miss Rate
- Increase associativity comes at the cost of increased hit time, and it outweight the time saved due to reduction in misses.

## Multilevel Caches to Reduce Miss Penalty
- Adding another level of cache to lessen the effect of miss penalty.
- Multilevel inclusion: L1 data are always present in L2 data. The second level cache must invalidate all the first level blocks that mapped onto the second level blocks to be replaced, hence increase first level miss penalty.
- Multilevel exclusion: L1 data never exist in L2 data. Typically a miss in L1 results in a swap of blocks between L1 and L2.
- The essense of cache design is balancing fast hits and few misses.
- The second level cache has fewer misses than the first level -> focus on fewer misses -> larger caches, higher associativity, larger blocks.

## Giving Priority to Read Misses to Reduce Miss Penalty
- With a write-through cache, the most important improvement is a write buffer of proper size.
- The write buffer might hold the updated value of a location needed on a read miss. On a read miss, check the content of the write buffer to find the needed data.
- With a write back cache, on a read miss, instead of writing the dirty block to memory first, copy the dirty block to a buffer, then read memory, and then write memory, so that read can finish sooner.

## Avoiding Address Translation During Indexing of the Cache to Reduce Hit Time
- Hit time is critical because it limits the clock cycle rate.
- Use virtual address instead of physical address to index the cache to reduce hit time.
- Need to copy protection information from TLB to keep page-level protection.
- When a process is switched, need to flush cache because one virtual address can map to different physical address. Add PID to tags to prevent the process from using other processes' data.
- OS and the program may use different virtual address for the same physical address, results in two copies of the same data in a cache.
- I/O works with physical address thus require address translation to interact with virtual cache.

# Summary
- Larger block size: incease miss penalty, decrease miss rate.
- Larger cache size: decrease hit time, decrease miss rate.
- Higher associativity: decrease hit time, decrease miss rate, more hardware.
- Multilevel caches: decrease miss penalty. Costly hardware.
- Read priority over writes: decrease miss penalty.
- virtual cache: decrease hit time.
