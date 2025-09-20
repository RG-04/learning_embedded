# RISC-V interrupts with OpenSBI timer example

This repository has an example of how to use interrupts in C with RISC-V. GCC tooling is assumed (this matters for things like `__attribute__` that the example depends on).

To build the binary, simply run:

```
make timer
```

The code for this repository is meant to accompany the article available at:

https://popovicu.com/posts/risc-v-interrupts-with-timer-example

## Key Learnings
- Here, we utilize more of SBI interface to now generate and set timer interrupts.
- SBI runs in M mode, and provides an interface to run in S mode.
- We use CSR (control/status registers) for the majority of this exercise.
- First, we enable S-level interrupts by setting certain bits of a CSR register.
- We are able to set the interrupt handler address as well. We mark the function as an S level handler for GCC to generate the relevant assembly code (return statements are sret vs mret, etc.)
- RDTIME is a pseudoinstruction that allows you to access a linearly increasing value (periodic as well).
- The timer interrupt also needs to be enabled separately. The relevant inline assembly can be seen in the code.
- Finally, in the handler itself, we set another timer which is set off eventually, and we see interleaved statements from the main and the handler.
- Interrupt handlers must be depicted as such by using the ```__attribute__``` keyword. This is also reflected in the generated assembly which backs up the registers in the stack using the sp initialized in the boot script.
- Here is the flow:
```bash
S-mode code (ecall) 
       │
       ▼
OpenSBI (M-mode) programs mtimecmp
       │
       ▼
Hardware timer counts (mtime)
       │
       ▼
mtime >= mtimecmp → hardware sets STIP
       │
       ▼
CPU sees pending interrupt + enabled SIE and STIE
       │
       ▼
CPU jumps to stvec → S-mode timer handler

```
