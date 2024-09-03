# Introduction
## What is Pipelining?
- Pipelining is an implementation whereby multiple instructions overlapped in execution.
- In the pipeline, different steps are completing different parts of different instructions in parallel.
- Pipelining yields a reduction in the average execution time per instruction.

## The Classic Five-Stage Pipeline for a RISC-V Processor
- Instuction Fetch(IF): Fetch the instruction from memory address given by PC. Increment the PC automatically.
- Instrution Decode(ID): Decode the instruction, read the registers according to rs, generate immediate.
- Execution(EX): The ALU operates on the operands prepared in the prior stage.
- Memory Access(MEM): Read from or Write to memory.
- Write-Back(WB): Write the result to register file.

- Introducing pipeline registers between successive stages of the pipeline. On the rising edge of the clock cycle, all the results from a given stage are stored in pipeline registers that is used as the input of next stage.
- Any value needed in on a later pipeline stage must be placed in pipeline register and copied from one to the next, until it is no longer needed.

# Pipeline Hazard
- Hazard prevent the next instruction from execution during its designated clock cycle.
- Three kind of hazards:
    - Structural Hazard: arise from resources conflit, occurs primarily in special purpose functional units, infrequent case.
    - Data Hazard: the instruction depends on the results of previous instruction.
    - Control Hazard: arise from branches and any other instructions that change the PC.
- Hazards can make pipeline stall, hence is a performance hit. Instructions before the stalled one must continue, after the stalled one must also stall.

## Data Hazards
- Assume instruction i occurs before instrution j and both i and j use register x, there are three types data hazards:
    - Read After Write: read of x by j occurs before the write of x by i, j would use the old value.
    - Write After Read: read of x by i occurs after the write of x by j, occur when instructions are reordered, never occur the simpe pipeline.
    - Write After Write: write of x by i occurs after write of x by j, occur when instrutions are reordered or when running times vary.

### Minimizing Data Harzads Stalls by Forwarding
- The ALU results from both EX/MEM and MEM/WB registers is fed back to ALU inputs. The Forwarding logic detects if it needs forwarding, control logic selects the forwarded results as ALU inputs.
- The forwarded results are first stored in register then feed to the functional units that need them.

### Data Hazards Requiring Stalls
- The load instruction has one clock cycle delay that cannot be eliminated by forwarding. CPU has pipeline interlock, which detects this hazard and stalls the pipeline until the hazard is clear.

### Overcomming Data Hazards With Dynamic Scheduling
- Dynamic scheduling eliminates data hazards that can not be avoid by forwarding. It depends on the hardware to reorder instructions execution to reduce stalls while maintaining data flow and exception behavior. Dynamic scheduling has several advantages at the cost of hardware complexity:
    - Transplantable: code that was compiled with one pipeline in mind can run effectively on a different pipeline, no re-compilation or multiple binaries needed.
    - It enables handling dependencies that was unknown at compile time.
    - It hides latency by executes other code while waiting on a cache miss.



## Branch Hazards
- When a branch is executed, it may or may not change the PC to PC + 4. The successive instructions are useless work if the branch were taken.
- There are four static compile time schemes for branch hazards:
    - Freeze or flush the pipeline: holding or deleting any instructions after branch until branch destination is known.
    - Treat every branch as not taken: Need to turn the fetched instruction into no-op if the branch is taken.
    - Treat every branch as taken.
    - Delayed branch: Put a delayed slot right after the branch, let compiler decide which instruction should be inserted. The instruction in the delayed slot will executed whether the branch is taken or not.

## Reducing the Cost of Branches Through Prediction
- As pipelines get deeper and the potential penalty of branches(delayed cycles) increases, using delayed branches become insufficient.
- We need more aggressive techniques for predicting branches.
- Static Branch Prediction: Use profile information collected from earlier runs to predict branches.
- Dynamic Branch Prediction: Predict branches dynamically based on program behavior.
- The deeper the pipeline, the worse the branch penalties in clock cycles.

# What Makes Pipeline Hard to Implement?
## Dealing With Exeptions
- In the pipeline, the overlapping of instructions makes it difficult to know whether an instruction can safely change the state of the processor or not.
- Exceptions may force processor to abort the instructions in the pipeline before they complete.

### Types of Exceptions and Requirements
- Synchronous v.s. Asychronous
    - Sychronous: The event happens as expected and is not affected by external factors.
    - Asychronous: Caused by external to the processor and memory, can be handled after the completion of the current instruction.
- User requested v.s. Coerced
    - User requested: User task directly ask for it, can always be handled after the instruction has completed.
    - Coerced: Caused by hardware event, not predictable.
- User maskable v.s. Nonmaskable: User maskable event can be disabled by a user task.
- Within v.s. Between instructions: Within: The event occurs in the middle of the instruction, usually sychronous. The instruction must be stopped and restarted.
- Resume v.s. Terminate: Whether the program will be terminated after the interrupt.

### Stop and Restarting Execution
- When an exception occurs, the pipeline control save the pipeline state correctly by:
    - Forcing a trap instruction into the pipeline on the next IF;
    - Prevent any state changes of the pipeline for the instructions that will not be completed before the exception is handled by placing zeros into the pipeline registers, starting from the faulting instruction and beyond.
    - Save the PC of the faulting instruction.
- The pipeline has precise exceptions if it can be stopped so that the instructions before the faulting instruction can be completed and those after it can be restarted from scratch.

### Exceptions in RISC-V
- Multiple exceptions may occur in the same clock cycle, and they can occur out of order. For example, instruction i occurs exception in MEM stage and instruction i+1 occurs exception in IF stage.
- To implement precise exception, must force exceptions occur in the unpipelined order.
- Every instructions hold exceptions until commit point(MEM stage), exception in the early stage overrides later exceptions for a given instruction, if trap is taken, invalidate any writes that was made by that instruction.

# Handle Multicycle Operations
- To support FP operations, the EX cycle may be repeated multiple times to complete the operation, and there may be multiple FP functional units. Like figure C.28. The shortcoming of this approach is obvious, the pipeline behind the repeating instruction will be stalled. Instead, FP adder and multiplier, divider is not pipelined. Insturctions now can have varying run times.

## Hazards and Forwarding in Longer Latency Pipelines
- Because instructions have varying run times, the number of write to register file can be larger than one in one cycle. WAWs are possible, because instructions no longer reach commit point(WB) in order(out-of-order completion), causing problems with exceptions. Longer latency of operations make stalls for RAW hazards more frequent.
- Three checks before an instruction can issue in ID stage:
    - Check for structural hazards
    - Check for RAW hazards
    - Check for WAW hazards

## Maintaining Precise Exception
- Out-of-order completion will result in imprecise exceptions.
- Buffer the result of an instruction until all the operations issued earlier are complete. Expensive to implement.
- Use a history file to keep track of original register value, used to rollback when occurs exceptions.
- Keep track of what operations were in the pipeline and their PCs. After hadling exceptions, software executes all the instructions that precede the latest completed instructions and restart the pipeline after the completed instruction.
- Allow the instruction issue only if it is certain that all the instructions before will complete without exception.
