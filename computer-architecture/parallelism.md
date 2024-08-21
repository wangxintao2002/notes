# Intro
- We have run into the pratical power limit for cooling commodity microprocessors. The power limit forced a dramatic change in the design of microprocessors.
- The improvement of response time of programs for desktop microprocessors has slowed. 
- Attention has been paid on improving throughput rather than decreasing response time by shipping microprocessors with multiple processors per chip.
# Instruction Level Parallelism
- Pipeline exploit the potential parallelism among instructions.
- Two primary methods to improve ILP:
    - Increse the depth of the pipeline -> shorter clock cycle, more operations overlapped.
    - Multiple issue: issue multipe instructions in one clock cycle -> CPI < 1.
        - Two main implementations(wether decisions are being made at compile time or runtime):
            > Static multipe issue
            > Dynamic multiple issue
        - Two responsibilities:
            > Packaging instructions into issue slots: how many instructions and which instructions can be issued in a given clock rate?
            > Dealing with data and control hazard.
        - Need extra hardware.
## Speculation
- Allows compilers or processors to guess about the properties of an instruction, to enable execution to begin for other instructions that may depend on the speculated instruction.
- Speculation mechanism includes both a method to check if the guess was right and a method to unroll the changes that was made by speculated instruction.
- The recovery mechanism:
    - In software(done by compiler): insert additional instructions that check the accuracy of the speculation and provide a fix-up routine to use when the speculation is wrong.
    - In hardware(done by processor): buffer speculative results. Correct -> write results. Wrong -> flush buffer, re-execute the correct instructions, flush or stall pipeline.
- The instruction being speculating may introduce exeption. If the speculation is correct, exeption should occur, but if it is wrong, exeption should not occur.
    - Compiler-based speculation: Ignore such exeptions until it is clear that they really should occur.
    - Hardware-based speculation: Exeptions are buffered until it is clear that the instruction causing them is no longer speculative.
## Static Multiple Issue
- Use compiler to assist with packaging instructions and handling hazards, static branch prediction and code scheduling for prevent hazards.
- Think of the issue packet as a single instruction with multiple operations.(Very Long Instruction Word)
- The use latency of load affects more instructions, ALU instructions that had no use latency now have one-instruction latency, since the results cannot be used in the paired instruction. Compiler scheduling techniques are neaded.
## Dynamic Multiple Issue
- AKA superscalar processors.The processor decides how many instructions can issue in a given cycle clock based on runtime conditions.
- Requires compilers to schedule the code, but is still different from VLIW:
    - The code is guaranteed by hardware to run correctly.
    - Compiled code will always run correctly independent of the issue rate or pipeline structure of the processor.
### Dynamic Pipeline Scheduling
- Chooses which instructions to execute while trying to avoid hazards and stalls.
- Three major unit(P&H Figure 4.69):
    - Instrucion fetch and issue unit: Fetchs, decodes, sends instructions to functional unit.
    - Multiple functional unit: has buffers, called reservation stations, which hold the operands and operation.Functional unit computes the result when the buffer contains all its operands then sends it to the commit unit or any functional units that needs it.
    - Commit uint: buffers the result until it is safe to put result into the registers or memory. Also used to supply operands, like forwarding logic in a statically scheduled pipeline.
- In-order issue, out-of-order execution, in-order commit.
# Data Level Parallelism
- Parallel achieved by performing the same operation on independent data(e.g. dealing arrays with for loop).
- SIMD
# Thread Level Parallelism
- Hardware multithreading: Typical multithreading technique.
- Hype-threading(SMT): Two or more copies of PC and register file in one core(utilizing the resources of multiple issue processors). SMT always executes instructions from multiple threads, leaving it up to hardware to handle dependences.
# Shared Memory Multiprocessor
- A  single pyhsical address across all processors.
- Two styles:
    - Uniform Memory Access(UMA): The latency of memory access is about the same no matter which processor requests the access.
    - Nonuniform Memory Access(NUMA): Main memory is devided and attached to different microprocessors or to different controller on the same chip.

# Hardware Synchronization
- Solution: Atomic read/write, read & write in single instruction
- Must use shared memory(otherwise no critical session).
- Common Implementation: Atomic swap of register <-> memory(e.g. amoswap.w.aq amoswap.w.rl), pair of instruction for "linked" read and write(e.g. lr.d sc.d).

# Cache Coherence
- One cache per processor.
- A memory system is coherent if:
    - A read by a processor P that follows a write by P, with no other writes by another processors always return the value written by P.
    - A read by a processor P that follows a write by another processor returns the written value if the read and write are sufficiently seperated in time and no other writes between two access.(need to deal with cache).
    - Two writes to the same location by any two processors are seen in the same order of all processors(serialized writes).
## Enforcing Coherence
- Track the state of any sharing of a data block cache.
- Protocol: snooping.
    - The caches are accessiable via some broadcast medium cache controller monitor on it to determine whether or not they have the requested copy.
    - Ensure exclusive access to data before writes it.(write-invalidate)
    - When read occurs, it misses cache, and is forced to fetch a new copy of the data.(from the cache that has the up-to-date data)
- Block size matters:
    - Bigger block size -> increasing bandwidth demands.
    - False sharing: two unrelated variable located in the same block.
