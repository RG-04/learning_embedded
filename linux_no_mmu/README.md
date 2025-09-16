# Linux kernel without an MMU
[Article to be followed](https://popovicu.com/posts/789-kb-linux-without-mmu-riscv/)
## Key Takeaways
- Linux can be built with a tiny configuration, which has no drivers or even an MMU
- We use the menuconfig functionality to disable (or enable) portions of the kernel while building.
- This build is good enough to run a single process, and writing directly to the UART port is possible (as seen in the code).
- Buildroot is a giant collection of Makefiles that allows you to easily build Linux images and comes in handy while creating your own distro. In this section I used only the cross compiler functionality.
- We cross compile using uLibC, which is a tiny implementation of C without an MMU
- bFLT is a lightweight executable format designed for systems with very limited resources. We cannnot use ELF anymore as that support is lost while removing the MMU.
- A key note is that we use the fPIC flag to get position independent code.
- Lastly, we use cpio compression to create a small initramfs filesystem, which is required to start the init process.