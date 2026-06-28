# ASH-8 Custom 8-bit CPU

A custom-designed 8-bit RISC processor with a handwritten assembler and compiler targeting the architecture

**Status:** In progress - Summer 2026

## Components:
- `docs/` - ISA specification and example programs (including compiled versions to be imported to logisim-evolution)
- `logisim/` - CPU circuit files, designed to be run in logisim-evolution
- `assembler/` - Python assembler (assembly --> binary)
- `compiler/` - Compiler (TBD high level language --> assembly)

## Architecture Notes
- Control unit is logic based, not ROM based