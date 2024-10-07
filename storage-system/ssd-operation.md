# Introduction
## SSD Organization
- Multiple cores, SSD controller, DRAM, NAND flash memory packages.
- In the SSD controller
    - Host Interface Layer(HIL)
    - Flash Translation Layer(FTL): data cache management, address translation, GC/Reflash
    - Flash Controller: ECC, Randomizer, connect to NAND flash packages
- In DRAM
    - Host Request Queue
    - Write Buffer
    - L2P mappings
    - Metadata

## NAND Flash Cell
- A flash cell can store multiple bits. A cell could leaks electrons overtime, or wears out after P/E cycles, lead to retention loss.
- **NAND String**: multiple cells are serially connected. Compose a bitline. 
- **Pages**: Multiple NAND string compose a wordline. A wordline is a page(or n pages, depends on how many bits a cell could store).
- **Blocks**: Multiple wordlines compose a block.
- **Planes**: A large number of blocks share bitlines in a plane.
- **Die**: A die contains multiple planes.

## NAND Flash Operation
- Read(a page)
- Erase(a block)
- Write(a page, must erase a whole block before writing)

# Reference
- OSTEP: flash-based SSDs
- ETH Modern SSD Architecture
