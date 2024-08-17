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

