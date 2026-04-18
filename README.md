# XV6 Page Table Lab

MIT 6.S081 Operating System Lab - Implementation of page table features in xv6 RISC-V.

## Overview

This repository contains the implementation of three tasks for the xv6 page table lab:

1. **Fast System Calls (ugetpid)**: Map a read-only shared page at USYSCALL to eliminate kernel trap overhead for getpid()
2. **Page Table Printing (vmprint)**: Recursive 3-level page table walker for debugging
3. **Page Access Detection (pgaccess)**: System call using RISC-V PTE_A bit to track accessed pages

## Quick Start

### Prerequisites

macOS with Homebrew:
```bash
brew install riscv64-elf-gcc
```

Linux:
```bash
sudo apt-get install gcc-riscv64-linux-gnu
```

### Build

```bash
cd xv6
TOOLPREFIX=riscv64-elf- make
TOOLPREFIX=riscv64-elf- make fs.img
```

### Run Tests

```bash
# Run in QEMU
make qemu

# Inside QEMU, run the test suite
$ pgtbltest
```

## Implementation Details

### Task 4.1: Fast getpid() via USYSCALL
- **Files modified**: `kernel/proc.c`, `kernel/proc.h`
- Maps a read-only page at USYSCALL containing `struct usyscall { int pid; }`
- User-space `ugetpid()` reads directly from this page, no system call needed
- Permissions: `PTE_R | PTE_U` (read-only, no write)

### Task 4.2: Page Table Printer
- **Files modified**: `kernel/vm.c`, `kernel/defs.h`, `kernel/exec.c`
- Recursively walks 3-level RISC-V page table (levels 2→1→0)
- Prints valid PTEs with physical addresses
- Called for init process (pid==1) to display initial page table

### Task 4.3: Page Access Detection
- **Files modified**: `kernel/sysproc.c`, `kernel/syscall.c`, `kernel/syscall.h`, `kernel/riscv.h`, `user/usys.pl`, `user/user.h`
- Defines `PTE_A` (bit 6) for RISC-V accessed bit
- Implements `pgaccess()` system call
- Returns bitmask of accessed pages, clears PTE_A after check

## Testing

The `pgtbltest` user program tests all three tasks:
- `ugetpid_test`: Verifies fast PID retrieval
- `vmprint_test`: Verifies page table output format
- `pgaccess_test`: Verifies accessed page detection

## References

- [MIT 6.S081 Course](https://pdos.csail.mit.edu/6.828/)
- [XV6 Book](https://pdos.csail.mit.edu/6.828/2023/xv6/book-riscv-rev3.pdf)
- [RISC-V ISA Specification](https://riscv.org/technical/specifications/)

## License

Based on xv6-riscv from MIT. See LICENSE in xv6/ directory.
