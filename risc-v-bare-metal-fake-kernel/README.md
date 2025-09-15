# RISC-V bare metal "fake kernel"

This code is meant to accompany the website article: https://popovicu.com/posts/risc-v-sbi-and-full-boot-process/

*Change your cross compile make prefix with `CROSS_COMPILE`*

There are 2 versions of the fake kernel:
1. The infinite loop one, which does nothing except running an empty infinite loop
2. The "hello world" one, which uses the OpenSBI calls to print to the UART

To build the first kernel, just run `make infinite_loop`. For the second kernel, run `make hello_world_kernel`.
To run, use `make run_{kernel_name}` to launch the qemu simulator. 

## Key Concepts

- **fw_dynamic_info**: QEMU uses this struct to pass kernel info to OpenSBI:
```c
struct fw_dynamic_info {
    unsigned long magic;      // FW_DYNAMIC_INFO_MAGIC_VALUE = 0x4942534f
    unsigned long version;    // Latest is 0x2
    unsigned long next_addr;  // Next stage address
    unsigned long next_mode;  // Next stage mode (e.g., 0x1)
    unsigned long options;    // OpenSBI options
    unsigned long boot_hart;  // Preferred boot HART (-1UL to skip)
};
```

- The kernel image is loaded as a separate ELF section in memory.  
- Registers can be inspected with GDB (`i r`), `a2` points to `fw_dynamic_info`.  
- QEMU populates this struct at power-on (in real hardware, this is done by the ZSBL).  
- OpenSBI exposes **extensions**:  
  - `a7` = extension ID (EID)  
  - `a6` = function ID (FID)  
  - `a0, a1, a2…` = parameters  

- Traps from the kernel automatically jump to OpenSBI handlers.

## Using GDB (gdb-multiarch)

Start QEMU in debug mode:
```bash
qemu-system-riscv64 -machine virt -bios none -kernel {kernel} -nographic -s -S
```
- `-s` → opens GDB server on port 1234  
- `-S` → freezes CPU at startup (waits for GDB)

Connect with GDB:
```bash
gdb-multiarch
set architecture riscv:rv64
set disassemble-next-line on
(gdb) target remote :1234
```


### Useful GDB Commands

- **Registers**:
```gdb
info registers        # Show all registers
i r                   # Shortcut for registers
```
- **Memory**:
```gdb
x/10x 0x80000000       # Examine 10 words at address
x/10xg 0x1028          # a2 in this context
```
- **Breakpoints**:
```gdb
break *0x80000000      # Break at specific address
continue               # Resume execution
step                   # Step one instruction
next                   # Step over
```
- **Inspect fw_dynamic_info**:
```gdb
p *((struct fw_dynamic_info *) $a2)  # Print struct pointed by a2
```
