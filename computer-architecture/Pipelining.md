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


