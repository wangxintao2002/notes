# Introduction
- Two changes made it easier to succeed with a new architecture(e.g. RISC):
    - The virtual elimination of assembly reduced the need for object-code compatibility.
    - The creation of vendor-independent OS lower the risk of making a new architecture.
- The RISC-based computer raised the performance bar, forced prior architecture to keep up.
    - Intel translated 80x86 instructions into RISC-like instructions internally.
- The hardware renaissance allowed modern programmers to trade performance for productivity.
    - In replace of perf-oriented language like C and C++, much more program today is done in managed language like Java and Scala.
- The limits of power forced the microprocessor industry to use multiple efficient processors instead of a single inefficient processor.
    - Switch from relying solely on ILP to DLP and TLP.
    - DLP and TLP are explicit parallel, a major burden of programmers.
- The improvements of processor performance has slowed down, due to:
    - The slowing of Moore's Law(transistors per chip double every year).
    - The unchanging power budgets for microprocessors.
    - The replacement of the single power-hungry processor with several enegy-efficient processors.
    - the limits to multiprocessing to achieve Amdahl's Law.
- The only path to improve is specialization.

# Defining Computer Architecture
## Instruction Set Architecture(The API)
- Design choice of ISA:
    - Class of ISA: register-memory ISA or load-store ISA.
    - Memory addressing: forcing objects aligned? byte or word addressing?
    - Addressing modes: How to calculate the address of operands.
    - Types and sizes of operands.
    - Operations.
    - Control flow instructions: Different ways to implement control flow instructions.
    - Encoding an ISA.
## Genuine Computer Architecture(The implementation of ISA)
- The implementation of a computer has two components: organization and hardware.
- Organization or microarchitecture includes memory system, and the design of CPU or processor.
- Hardware refers to the detailed logic design.

# Quantitative Principles of Computer Design
- The advantage of parallelism: 
    - System level: multiple processors and multiple storage devices.
    - Processor level: Pipelining.
    - Detailed degital design: multiple banks of memory.
- Principle of Locality.
- Focus on the common case.
- Amdahl's Law: The perfomance improvement to be gained from using some faster mode for execution is limited by the fraction of time the faster mode can be used.
    - $Execution time_new=Execution time_old \times ((1-Fraction_enhanced)+\frac{Fraction_enhanced}{Speedup_enhanced})$
    - Serve as a guide to how much an enhancement will improvement performance.
- The processor performance equation: $CPU time = \frac{Instructions}{Program} \times \frac{Clock cycles}{Instruction} \times \frac{Seconds}{Clock cycle}$
