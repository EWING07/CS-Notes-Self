# MIT 6.828: Operating System Engineering

[Course Website](https://pdos.csail.mit.edu/6.828/2020/schedule.html), [知乎参考](https://zhuanlan.zhihu.com/p/74028717?utm_source=wechat_session&utm_medium=social&utm_oi=619085716488065024&utm_content=sec)

## Chapter 1 Operating System interfaces
1. share a computer among multiple programs and to provide a more useful set of services than the hardware alone supports.
2. shell is a user program and not part of the kernel. The xv6 shell's implementation can be found at ([user/sh.c:1](https://github.com/mit-pdos/xv6-riscv/blob/riscv//user/sh.c#L1))
3. Process and memory
   1. xv6: user-space memory(instructions, data, and stack) and per-process state private to the kernal.
4. I/O and File descriptors
   1. a small integer representing a kernel-managed object that a process may read from or write to.
   2. file descriptor 0 (standard input), writes output to ﬁle descriptor 1 (standard output), and writes error messages to file descriptor 2 (standard error).
5. Pipes
   1. advantages:
      1. 自动清理
      2. 可以任意传输长数据
      3. 允许并行的流水线
6. File System
   1. inode: the same underlying file.