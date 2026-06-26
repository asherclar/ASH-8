# Example Programs
Syntax:
```
    MNEMONIC Rd, Rs1, Rs2     ; R-type  (e.g.  ADD R3, R1, R2)
    MNEMONIC Rd, Rs1, IMM6    ; I-type  (e.g.  ADDI R1, R1, -5)
    MNEMONIC ADDR12           ; J-type  (e.g.  JEQ 14)
    STORE Rs1, Rs2            ; STORE only behaves like this because no destination register
```

*Adding 15 and 10*
```
    LI   R1, 15        ; R1 = 15
    LI   R2, 10        ; R2 = 10
    ADD  R3, R1, R2    ; R3 = 25
    STORE R0, R0, R3   ; RAM[0] = 25
    HALT
```

*Count down from 10*
```
    LI   R1, 10        ; R1 = 10
    loop:              ; assembler resolves this to address 1
    SUBI R1, R1, 1     ; R1 = R1 - 1
    JNE  loop          ; jump to loop if R1 != 0
    HALT
```

*Store to and reload from memory*
```
    LI    R1, 42     ; R1 = 42 (value)
    LI    R2, 0      ; R2 = 0  (address)
    STORE R1, R2     ; RAM[0] = 42
    LOAD  R3, R2     ; R3 = RAM[0] = 42
    HALT
```