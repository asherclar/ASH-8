# ASH-8 Instruction Set Architecture
### Version 0.1 - 6/18/2026

## Overview
ASH-8 is a custom 8-bit RISC processor by Asher Clar made in the summer of 2026. It features a 16-bit fixed-width instruction format, 8 general-purpose registers, and a complete instruction set capable of running compiled programs.
This document is the authoritative reference for the architecture, and all parts (logisim circuit, assembler, and compiler) will follow this spec.

## Architecture Parameters
Data width - 8 bits

Instruction width - 16 bits

Address space - 12 bits (4KB)

General registers - 8 (R0-R7)

R0 behavior - hardwired to 0x00; ignores writes

Data structure - Harvard (separate instruction/data)

Number system - Two's complement

Endianness - Big-endian

## Instruction Format

[15:12] Opcode (OPC) - 4 bits - designates which operation will be done

[11:9] Destination Register (Rd) - 3 bits - designates where the output of the operation will go

[8:6] Source Register 1 (Rs1) - 3 bits - the first input

[5:3] Source Register 2 (Rs2) - 3 bits - the second input

[2:0] Functional Field (FUNC) - 3 bits - gives context to operation

For instructions that need an immediate value (a hardcoded number) instead of a second register, Rs2 and FUNC merge into a single 6-bit immediate field:

[15:12] OPCODE - 4 bits

[11:9]  Rd - 3 bits

[8:6]   Rs1 - 3 bits

[5:0]   IMM6 - 6 bits (signed, range -32 to +31)

For jump instructions, Rd + Rs1 + Rs2 + FUNC all merge into a 12-bit address field:

[15:12] OPCODE - 4 bits

[11:0]  ADDR12 - 12 bits (addresses 0x0000–0x0FFF)

NOTE: Programs must be loaded starting at address 0x0000 and must not exceed 4096 instructions. For jumps beyond this range, use JMPR (see OPC 1101)

## FLAGS Register
Z - Zero flag (bit 0). Set to 1 when the result of the last operation was exactly 0x00. 

N - Negative flag (bit 1). Set when bit 7 of the result is 1, meaning the result is negative in two's complement. Used to implement less-than comparisons.

C - Carry flag (bit 2). Set when an addition produces a carry out of bit 7, meaning the result overflowed an unsigned 8-bit value. 255 + 1 sets the carry flag. Will be used for multi-byte arithmetic.

V - Overflow flag (bit 3). Set when a signed addition or subtraction produces a result that's too large for 8 bits to represent correctly. Specifically useful for signed overflow.

Memory operations must leave these flags unchanged; only ALU operations (and some IMM6 ops) can change them

# Instruction Definitions
## Register Operations
### OPC 0000: Register-based ALU Operations
####    - FUNC 000: ADD
        Rd = Rs1 + Rs2
        addition; sets Z, N, C, V
####    - FUNC 001: SUB
        Rd = Rs1 - Rs2
        subtraction, implemented as `Rs1 + (~Rs2 + 1)`; sets Z, N, C, V
####    - FUNC 010: AND
        Rd = Rs1 & Rs2
        bitwise AND; Sets Z, N. C and V are cleared
####    - FUNC 011: OR
        Rd = Rs1 | Rs2
        bitwise OR; Sets Z, N. C and V are cleared
####    - FUNC 100: XOR
        Rd = Rs1 ^ Rs2
        bitwise XOR, which enables a check for 0 since Z of equal values will equal 0; Sets Z, N. C and V are cleared
####    - FUNC 101: NOT
        Rd = ~Rs1
        bitwise NOT, ignoring Rs2. Sets Z, N. C and V are cleared
####    - FUNC 110: SHL
        Rd = Rs1 << 1
        Shifts digits left one bit, bit 0 becomes 0, and bit 7 shifts to carry flag. Equivalent to multiplying by 2. Sets Z, N, C, clears V.
####    - FUNC 111: SHR
        Rd = Rs1 >> 1
        Shifts digits right one bit, bit 7 becomes 0, bit 0 shifts to carry flag. Equivalent to dividing by 2 (unsigned). Sets Z, N, C, clears V
## Immediate Value Operations
### OPC 0001: ADDI
    Rd = Rs1 + IMM6
    Adds a signed 6-bit constant to Rd, sign-extended (copies most significant bit, bit 5 fully to left, bits 7 and 6) to 8 bits before addition. Sets Z, N, C, V

### OPC 0010: SUBI
    Rd = Rs1 - IMM6
    Subtracts signed 6-bit constant (sign-extended to 8 bits) from Rd. Sets Z, N, C, V.

### OPC 0011: LI
    Rd = IMM6
    Loads 6-bit signed constant (sign-extended to 8 bits) to a register and ignores Rs1. Does not affect FLAGS, including Z or N

## Memory Operations
### OPC 0100: LOAD
    Rd = RAM[Rs1]
    Reads one byte from RAM and stores it into Rd. Does not affect FLAGS, and ignores Rs2 and FUNC

### OPC 0101: STORE
    RAM[Rs2] = Rs1
    Writes byte in Rs1 to memory at the address in Rs2. Does not affect FLAGS, and ignores Rd and FUNC

## Jump Operations: PC *ends* @ address; PC doesn't increment. Also, can only reach first 4096 instructions despite 16 bit address space
### OPC 0110: JMP
    PC = ADDR12
    Unconditional jump

### OPC 0111: JEQ
    if Z=1: PC = ADDR12
    Jump if last result was zero, allows conditional logic

### OPC 1000: JNE
    if Z=0: PC = ADDR12
    Jump if last result was not zero

### OPC 1001: JLT
    if N=1: PC = ADDR12
    Jump if last result was negative; can implement less-than comparisons

### OPC 1010: JGE
    if N=0: PC = ADDR12
    Jump if last result was non-negative; can implement greater than or equal to comparisons

### OPC 1011: JCS
    if C=1: PC = ADDR12
    Jump if carrry was set, helps detect overflow and enables multi-byte arithmetic

## Control Operations
### OPC 1100: NOP
    PC = PC + 1
    No operation

### OPC 1111: HALT
    Ends the program

### OPC 1101 and 1110: Reserved for future expansion

# Reset Behavior
On power-on/reset:

PC = 0x0000, All general-purpose registers undefined, RAM undefined, FLAGS undefined and will be set on first ALU instruction

