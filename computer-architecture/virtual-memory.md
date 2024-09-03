# Introduction
- Modern computers run multiple processes concurrently, each process has its own address space, it is expensive for them to store all running processes' address space or all of the address space of one process at the same time. Pratically, not all code and data have to be in memory before a program can begin. 
- Virtual memory comes to save the day:
    - Virtual memory help share protected memory space between processes.
    - Virtual memory automatically manages two levels of memory hierarchy represented by main memory and secondary storage.
    - Virtual memory allows program to be placed anywhere in physical memory or disk by using mapping mechanism(page table in paged system).

# Four Memory Hierarchy Questions Revisited
- What Can a Block be replaced in Main Memory?
    - The miss penelty for virtual memory is unaffordablly high, so the placement algorithm with low miss rate is imperative. Thus the OS can put a block anywhere in phtsical memory.
- How Is a Block Found If in Main Memory?
    - The OS maintain a data structure called page table, which manifests the mapping of virtual address and physical address.
- Which Block Should be Replaced on a Virtual Memory Miss?
    - The OS typically uses LRU replacement algorithm.
- What Happens On a Write?
    - Because of the cost of an unneccessary access to the next low level is high, the write strategy is always write back.

# Techniques for Fast Address Translation
- Page table are stored in memory and sometimes paged themselves, this means that every access to memory involves at least two memory access: one for getting translating virtual address by consulting page table, one for accessing the needed data. Using a special cache(TLB) that keeps recently accessed PTEs avoids the first memory access for translating. 
