# Remote Procedure Call
    - Making remote looks local.
    - Hiding the complexity of the network.

# Client-Server Model
    - Server exports interfaces, client invokes them.
    - Server acts as an ADT(abstract data type).

# Historical Roots of RPC
    - Client and server on different machines.
    - Potential parallelism.
    - No reason for client to block when asking service from server.
    - Early usage of message passing fails.
        - A sends invocation message to B.
        - A does other work(often little to do).
        - A polls for B's reply or is interrupted.
        - A receives B's reply.

# Characteristic of RPC
    - Control flow(synchronous transfer of control)
        - Caller make requests and blocks.
        - Calle sesssces request and replies. 
        - Caller resumes.
    - Invocation syntax
        - y = foo(cid, x1, x2, ...)
        - cid is connection handle.

# RPC Transport
    - Ideal environment: no failures, 1 invocation -> 1 execution of calle.
        - In-order delivery of packets with predictable delays.
        - Perfect server hardware and bug-free software.
    - Real environment: hardware/software failures, network failures...
        - Packet loss, corruption, re-ordering, unpredictable delays.
        - Flaky hardware and buggy software.

## Handling Flakiness
    - Outsource, let someone else handles it.
        - Use TCP, layer RPC on top of it
            > TCP guarantees: reliable delivery, in-order delivery, unlimited data size.
            > TCP NOT guarantees: data received will be the same size as it was sent.
    - Do it yourself(using UDP).
        - Retransimission. 
            > send request and start a timer
            > if no reply when timer goes off, retry.
            > finally give up and declare failure.
        - Problem with retransmission.
            > server is still computing or it is overloaded.
            > reply losts.
            > duplicate execution.

# Timeouts in Distributed Systems
    - How do you pick a perfect timeout value?
        - in the worst case, no perfect value exists.
        - reply could arrive just after you give up.
        - but in average case, it is much better.
    - What should server do when it sees a duplicate?
        - reply lost.
        - reply is on the fly.
        - compute time was excessive.
        - client was too impatient.
        - ... 
        - the best server can do is to retransimit reply.
    - Knowledge at server is always stale relative to client and vice versa(?).
    - Rplies must be preserved(1 reply saved per connection). 

# Exactly-once Semantics[1]
    - How long to keep old replies and sequence numbers?
        - keep last reply for every client until next request comes.
        - must survive server crashes -> write info to disk -> increase latency.
        - clean undo of partial computations before crash(Atomic operation).
    - A RPC would have exactly-once semantics if:
        - success return from RPC call -> call executed exactly once.
        - call blocks indefinitely, no failure return(no return, no failure). 
    - Not appropriate for many real applications.

# At-most-once Semantics[1]
    - compare to exactly-once, client must consider timeout.
    - Avoid indefinite blocking by timeout.
    - A RPC would have at-most-once semantics if:
        - success -> call executed exactly once.
        - timeout -> call executed once or not at all. 
    - Servers may be sluggish or unreachable
        - complicates setting of timeout value.
        - probes to check server health during long calls.
        - server responds with busy if still working.
        - essentially a keep alive mechanism.
    - Server recovered from crash has no responsibilities for retransimitted requests.

## Orphaned Computations
    - client gives up while server continues working on useless job(server may hold resources, slowing other works).
    - orphan detection and extermination are difficult. 

# At-least-once Semantics
    - requires operation idmpotency.
    - implementation can be completely stateless on server.

# Safety and Liveness Properties
    - safety: bad things don't happen.
    - liveness: timely execution progress.Good things will happen.
    - Real-world should balance safety and liveness.

# What can TCP do for RPC?
    - TCP timeout -> still have to reconnect.
        - new TCP conn unaware of old, server must handle duplication.
        - orphans possible.
        - exactly once no easier.
    - TCP can simplify at-most-once RPC.
        - Timeout handling can be out of band(e.g. phone call), avoids retransmission and duplicate elimination. TCP does that.
        - on TCP failure, declare RPC failure, high level invokes out of band mechanism to handle failure.
    - TCP hurts best-case performance.
        - TCP use 4 packets for best case.
        - UDP only uses 2.

# End-to-End Reasoning[2]

# Why are Distributed System Slow?


# References
[1] OpenAI. (2024). ChatGPT [Large language model]. https://chatgpt.com/c/a95c584c-803c-423b-ab47-914392e46d28
[2] Saltzer J H, Reed D P, Clark D D. End-to-end arguments in system design[J]. ACM Transactions on Computer Systems (TOCS), 1984, 2(4): 277-288.
