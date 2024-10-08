# Programming for high performance
- Optimizing the performance is an iterative process of refining choices for decomposition, assignment and orchestration. The key goals is:
    - balance workload onto availiable execution resources.
    - Reduce communication to avoid stalls.
    - Reduce extra work(overhead) performed to increase parallelism, manage assignment, reduce communication.
- Always implement the simplest solution first, then measure performance to determin if need to do better.

# Balancing the workload
- Only small amount of load imbalance can significantly bound maximum speedup.
- Static assignment: assign equal number of work to each thread(blocked or interleaved). Simple, zero overhead. Applicable when the cost of work and the amount of the work is predictable.
- Semi-static assignment: Pirodically profiles application and re-adjusts assignment.
- Dynamic assignment: Determine assignment dynamically at runtime.
    - May use a shared work queue, threads pull task from the queue.
    - May suffer from synchrounization overhead, use coarse granularity partitioning to decrease lock contention overhead(each thread has more work to do). 
    - Many small tasks enables good workload balance but increase overhead of managing assignment(sync overhead). Ideal granularity depends on many factors.
    - If long task is scheduled to be issue last, it will lead to load imbalance. Need to discover it and schedule long tasks first to balance the load.
    - May use multiple work queues, threads pull task from own work queue, if own work queue is empty, steal tasks from others. Costly synchronization occurs when stealing. All dependencies need to be satisfied for a task to be assigned to a worker thread.

## Scheduling fork-join programs
- Using pthread_create to launch independent work, and use pthread_join to syncronize.
- Heavyweight spwan operation, many more threads than cores, leads to context switching overhead and larger working set, less cache locality.
- Consider execution at point of spawn of foo() and then call bar(), What thread should execute foo()? What thread should execute bar()? 
    - If there's a idle thread: run bar() on caller thread and run foo() on another thread or run foo() on caller thread and run bar() on another thread.
    - If no thread is idle: run foo() first via function call then returns from foo() and run bar(). This is inefficient because other threads may become idle and could be performing bar() at this time. To address this issue, use work queues.
 - Per-thread work queues store tasks. At each spawn, system stores path that is not executed to work queue. Consider spawn foo(i) in a for loop.
    - Run continuation first: Caller thread spawns work for all iterations before executing any of it. Need O(N) of space for spawned work.
    - Run child first: Caller thread put continuation on work queue for other threads to steal. If stealed, stealing thread spawns and executes the next iteration.
- [case study]: Scheduling quicksort, assuming 200 elements. Run child first, put continuation on the work queue, finally end up having queue[0] = cont:101-200, queue[1] = cont:51-100, queue[2] = cont:26-50 and caller thread working on 0-25. Other threads should steal tasks from front, why?
    - Locality: caller thread is working on 0-25, it is likely that cont:26-50 are on the L1 cache.
    - Biggest work is on the top of the queue, steal from front avoid stealing thread from stealing frequently.
    - Local thread pull task from tail and stealing thread steal task from front. Reduces contention between local thread and stealing threads.

## Pool of worker threads
- All threads created at application launch, exactly as many threads as execution contexts in the machine. Each thread pulls task from worker queue if work exists.

## Divide-and-conquer parallelism
- Instead of spawning work in a for loop sequentially, generate work in parallel using recursive_for. If the range of the input is less than a particular granularity, spawns work in a for loop, else divide the range in half and spawns work responsible for one of them recursively.

# Minimize communication cost
- Each thread has its own address space, threads communication and synchronize by sending/receiving messages. Block or non-block send and receive.
- Using non-block send/receive to pipeline the communication to reduce the cost, A general model of communication:
    - Total communication time = overhead + occupancy + network delay
    - Total communication cost = communication time - overlap
    - overlap: portion of communication performed concurrently with other work.
    - overhead: time spend on the communication by a processor
    - occupancy: time for data to pass through slowest component of system
    - network delay: everything else
    - occupancy determines communication rate.
- Communication-to-computation ratio: the ratio between the amount of communication and the amount of computation.
    - Think of communication between:
        - a processor and its cache.
        - processor and memory.
        - processor and remote memory.
- Inherent communication: information that must be moved between processors to carry out the algorithm given the specified assignment.
    - Example: computation operands are located in remote memory.
    - Good assignment decisions can reduce inherent communication.
- Artifactual communication: all other communication, results from system implementation.
    - System has minimum gruanularity of data transfer, or the same data communicated(load from memory) multiple times due to cache capacity miss.
    - Need to expolit spatial locality with proper granularity of communication/data transfer and cache stuff.

## Techniques for reducing communication
- Improving cache temporal locality by changing the order of the load inputs
or fusing loops(combine multiple loops into one loop).
- Improve arithmetic instensity by sharing data. Gather the tasks that operate on the same data, threads load all the data the share to the shared memory and work on it.

## Summary of reducing communication cost
- Reduce the overhead of sender/receiver: fewer and larger messages(amortize overhead), coalesce many small messages into large ones.
- Reduce latency of communication: reconstruct code to exploit locality or improve hardware communication architecture.
- Increase communication/computation overlap: use async communication, pipelining, multi-threading, prefetching, additional concurrency(more concurrency than exec unit) to hide latency.

# Contention
- Occurs when many requests to a resource are made whithin a small window of time(access to memory, lock, etc).
- Ways to reduce contention: replicate contended resources(e.g. local copies, fine-grained locks).

# Performance analysis strategies
- Always, always try the simplest parallel solution first, the measure performance to see where you stand.
- Insert assertions and logs in code, test programs with 1 thread or 2 threads first.
- Determin if performance is limited by computation, memory bandwidth or synchrounization.
- If exec time increase linearly with operation count as non-memory instruction are added, then the code is instruction-rate limited.
- If exec time decrease not much with removing all non-memory instruction but load same data, then there is memory bottleneck.
- Change all array access to A[0] to see an upper bound on benifit of improving locality of data access.
- Rmove all atomic operations or locks establishes an upper bound on benifit of reducing sync overhead.
