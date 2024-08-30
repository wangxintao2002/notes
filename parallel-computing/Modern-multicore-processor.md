# Paralell Execution
- Making a single fancy processor(with fancy branch predictor, memory pre-fetcher, etc.) that make instruction stream run faster is hard. It keeps programmer away from thinking about parallelism.
- Instead, use multiple simpler cores. Each core becomes slower, but now multiple cores can run in parallel. But if the program expose no parallism, it will run slower in multicore processor. Programmer should exprssing parallelism in the program(e.g. using multi-threading).
- Using SIMD(within a core), add ALUs to increase conpute capability, same instruction broadcast to all ALUs, executed in parallel on all ALUs. Share an instruction stream(i.e multiple data are operated under the same type of operation).
    - Explicitly SIMD: using instrinsics.
    - Implicitly SIMD: hardware automaticly doing SIMD, might causing divergent execution.
- Using multicore processor and SIMD operation together. For example, parallel the for loop, and in every iteration apply SIMD instruction.
    - If there is conditional execution(e.g. if-else) in the loop, execute each group of branch in order.

## Terminology
- Instruction stream coherence("coherent execution")
    - Same instruction sequence applied to all elements operated upon simultaneously.
    - Necessary for efficient use of SIMD processing resources.
    - Not Necessary for efficient parallelization across cores, since each cores can fetch a different instruction stream.
- Divergent exeution: lack of instruction stream coherency.

# Accessing Memory
- Use cache to reduce memory access latency.
- Use prefetching to reduce stalls(hide latency).
- Use multi-threading to reduce stalls(hide latency).
    - Key idea of throughput-oriented systems: Potentially increase time(time that stalled thread become runnable waiting for being scheduled) to complete work by any one thread, in order to increase system throughput when running multiple threads.
    - Hardware-supported multi-threading:
        - Each core has multiple execution contexts for multiple threads, processor is responsible for scheduling threads, not OS.
        - Interleaved multi-threading: one thread per clock.
        - Simultaneous multi-threading(SMT): multiple threads per clock.
## GPUs: Extreme throughput-oriented processors
- Example on GTX 480:
    - SIMD unit operate 32 elements at a time(called "warps", sharing an instruction stream), 48 warps simultaneously interleaved, over 1500 elements can be operated concurrently by a core, up to 15 cores. 23000 pieces of data processed concurrently.
    - In GPUs, assume caches are not going to help, focus on hide latency by multi-threading to keep cores busy. Minimize all fancy stuff to leave room for ALUs.
- Experiment: Element-wise multiplication of two vectors A and B. Assume vectors contain millions of elements. Load input A[i] and B[i], compute, store to C[i].
    - Three memory operations(12 bytes) for every MUL.
    - 480 GPU can do 480 MULs per clock(@ 1.2GHz).
    - Need ~6.4TB/s of bandwidth (12 x 480 x 1.2G) to keep functional units busy(only have 177GB/s).
    - ~3% efficientcy(177GB/s / 6.4TB/s), but 7x faster than 2.6GHz quad-core CPU that connected to 25GB/s memory bus.
- Bandwidth limited: processor request data at a too high rate, the memory can't keep up. No amount of latency hiding helps.
- Example on NVIDIA V100 GPU:
    - One core includes 4 sub-core, each sub-core contains 16 SIMD fp32 units, SIMD int units, 8 SIMD fp64 units, Tensor core unit and Load/store unit. 64 warps in total, each sub-core is capable of scheduling and interleaving up to 16 warps. Each warp has execution context for 32 CUDA threads, executed in a SIMD manner(SIMT, single instruction multiple CUDA thread). 128KB of shared memory and L1 cache.
    - A V100 GPU has 80 unit described above.

# Summary
- Three major ideas that modern processors employ to varying degrees
    - Multiple cores
    - SIMD operations
    - Multi-threading
- Many parallel application are bandwidth bound.
- GPU architecture use same technique as CPUs except that GPUs push these concepts to extreme scales.
