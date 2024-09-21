# Introduction
- Three phases of a synchronization event: acquire method, waiting algorithm, release method.

# Implement locks
- Test-and-set based lock:
    - ts R0, mem[addr]  // load mem[addr] into R0, if mem[addr] is 0, set mem[addr] to 1

    - lock:  ts R0, mem[addr]
             bnz R0, lock
      unlock: st mem[addr], #0
    - Test-and-set based lock suffers cache coherence traffic. Test-and-set instruction issues BusRdX operation, which invalidates all of the other copies. When one processor holds the lock, the other processors keep issuing BusRdX operations, which lead to bus traffic.
- Desirable lock performance characteristics:
    - low latency
    - low interconnect traffic
    - scalability
    - low storage cost
    - fairness
- Test-and-test-and-set based lock:
    - void lock(int * lock) {
        while(1) {
            while (*lock != 0);
            if (test_and_set(*lock) == 0)
                return;
        }
      } 
    - void unlock(volatile int *lock) {
        *lock = 0;
      }   
    - Turning BusRdX to BusRd.
    - Slightly higher latency but generates much less traffic.

# Implement atomic fetch-and-op
- atomic compare-and-swap(CAS): if the value at address X is equal to the value to be compare, set the value at address X to new value val, and return the old value, else return the old value only and do nothing.
- Can implement atomic fetch-and-op out of atomic CAS

# Fine-grained locking
- Enable parallelism in data structure operations, but has overhead of taking a lock each traversal step, extra storage cost.

