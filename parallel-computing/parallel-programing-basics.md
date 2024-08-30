# Creating a parallel program
- Decompose the problem to subproblems, assign those subproblems to multiple parallel threads, coordinate threads to do the job correctly(communication, dependencies), finally map threads to actual hardware units.

# Decomposition
- The key of decomposition is identifying dependencies.
- Amdahl's Law: dependencies limit maximum speedup due to parallelism. Let S be the fraction of sequential execution that is inherently sequential, then maximum speedup due to parallel execution is <= 1 / S, that is speedup <= $\frac{1}{S + \frac{1 - S}{P}}$.(P is # of processors)
    - [case study]: Consider a two-step computation on a NxN image. step 1: double brightness of all pixels. step 2: compute average of all pixel values. Each step takes ~N^2 time. Parallelly execute step 1 on P processors, takes N^2 / P time, then compute partial sums in parallel, combine results serially, takes N^2 / P + P time.
- Programmer is responsible for performing decomposition. Automatic decomposition remains a challenge research.

# Assignment
- Assign tasks to workers/threads, balance workload, reduce communication casts, can be performed statically or dynamically during execution.
    - Statical assignment: creating pthreads and assign.
    - Dynamical assignment: Language/runtime assign tasks, and worker thread assign itself the next task when finishing current task.
    - [case study]: dynamic assignment using 4 threads is 100x faster than spwan 10000 threads and statically assign.

# Orchestration
- Involves:
    - Structuring communication
    - Adding synchronization to preserve dependencies if neccessary.
    - Organizing data structures in memory.
    - Scheduling tasks.
- To reduce costs of communication/sync, preserve locality of data reference. If synchrounization is expensive, might use it more sparsely.

# Mapping
- Mapping threads to hardware execution units. Can be mapped by OS, compiler or hardware.
