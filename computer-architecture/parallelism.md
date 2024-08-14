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
