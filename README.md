# ASH-8 Custom 8-bit CPU

A custom-designed 8-bit RISC processor in logisim-evolution 4.1.0 with a handwritten assembler and compiler

## Status:
*Phase 1: Hardware Design:* **In Progress June 2026**  
*Phase 2: Assembler:* **Yet to start**  
*Phase 3: Compiler* **Yet to start**

## Files:
- `docs/` - ISA specification and example programs 
- `logisim/` - CPU circuit files, designed to be run in logisim-evolution 4.1.0, and assembled versions of the example programs in `docs/`
- `assembler/` - Python assembler (assembly --> binary)
- `compiler/` - Compiler (TBD high level language --> assembly)

## Architecture Notes
- Control unit is logic based, not ROM based