# Accessing Shared Variables
- Compiler optimization might be harmful, it assumes the optimized variables are neither accessed nor modified by any other thread. This assumption allows the compiler to carry out optimizations like:
    - Load/store tearing: compiler uses multiple load instructions for a single access.(e.g. access a 64-bit variable on a 32-bit machine), resulting to partial updates.
    - Load/store fusing: 
