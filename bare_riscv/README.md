# Bare Metal RISC-V – Personal Notes

- Target: run a bare metal program on `riscv64 virt` in QEMU.  
- Goal: send 'hello' over UART without OS or libraries.

## Boot Process

- QEMU simulates power-on and loads memory at `0x1000` first (ZSBL).  
- ZSBL sets up minimal state and jumps to `0x80000000` (where our code starts).  
- `-bios` loads initial firmware (OpenSBI by default).  
- `-kernel` is loaded by the BIOS/ZSBL into memory, ELF format.  

## Bare Metal Programming

- UART is memory-mapped; writing to its registers sends data.  
- Program flow:
  1. Initialize UART.
  2. Loop over string and write chars to UART.  
  3. Ensure PC starts at `0x80000000`.  

## Running

- Compile with RISC-V cross-compiler.  
- Run QEMU:
```bash
qemu-system-riscv64 -machine virt -bios {myprogram} -nographic
```
- Output appears on terminal.  

## Key Takeaways

- ELF structure is important for memory layout.  
- UART gives basic I/O without OS.  
- Understanding boot stages (ZSBL → BIOS/OpenSBI → kernel) is crucial.  
- Bare metal programming = direct hardware control, no OS abstractions.  
