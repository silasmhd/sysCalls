# System Call Enhancements for xv6

## Description
This project adds two system calls to xv6, `trace()` for system call tracing with a bitmask, and `sysinfo()` for querying kernel statistics (free memory, process count)

## Objectives
- Implement `trace(mask)` to enable per-process syscall tracing inherited by children
- Implement `sysinfo(struct sysinfo*)` to return free memory and process count
- Add syscall infrastructure (numbers, stubs, dispatch, argument handling)

## Implementation
- **trace(mask)** — Store mask in `proc` struct; inherit in `fork()`; in `syscall()`, if bit for current syscall is set, print PID, syscall name, return value
- **sysinfo(info)** — Collect free memory from kalloc, process count from proc table, `copyout()` struct to user space

## Build and Run Instructions

To build and run xv6, you need a RISC-V toolchain and QEMU emulator

I used WSL and ran Ubuntu 24.04 specifically, so there may be differences with different distributions and versions. Once you have wsl installed this will install the version of Ubuntu I used, but removing "-24.04" will install the latest.

```bash
wsl.exe --install Ubuntu-24.04
```

Next update and install the packages I used

```bash
sudo apt-get update && sudo apt-get upgrade

sudo apt-get install git build-essential qemu-system-misc gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu  
```

Lastly clone this repository, change directory into it, and run QEMU.

```bash
git clone https://github.com/silasmhd/sysCalls

cd sysCalls

make qemu
```

To exit QEMU press `ctrl + a` then `x`. 
_This won't work with capslock on._

## Tests

Once you have QEMU running, you can run the test for this project and look at the them in `test-syscall`.

```bash
make test
```

## Files Modified
- `kernel/syscall.h` — `SYS_trace`, `SYS_sysinfo` syscall numbers
- `kernel/syscall.c` — `syscall()` tracing logic, syscall name array
- `kernel/sysproc.c` — `sys_trace()`, `sys_sysinfo()` implementations
- `kernel/proc.h` — `trace_mask` field in `struct proc`
- `kernel/proc.c` — copy trace mask in `fork()`, count non-UNUSED processes
- `kernel/kalloc.c` — function to return free memory bytes
- `kernel/sysinfo.h` — `struct sysinfo` definition
- `user/usys.pl` — stubs for `trace`, `sysinfo`
- `user/user.h` — prototypes, `struct sysinfo` predeclaration
- `Makefile` — add `_trace`, `_sysinfotest` to `UPROGS`

## References
- xv6 book, Chapter 2: Operating System Organization, and Chapter 4: Traps and System Calls
- RISC-V Privileged Architecture Specification (system call interface)
- Original xv6-riscv repository: https://github.com/mit-pdos/xv6-riscv
