# Operating Systems Three Easy Pieces

### Topic: Virtulaization, Concurrency, Persistence
[Textbook](http://pages.cs.wisc.edu/~remzi/OSTEP/),[BookCode](https://github.com/remzi-arpacidusseau/ostep-code),[Projects](https://github.com/remzi-arpacidusseau/ostep-projects),[Homework](https://github.com/remzi-arpacidusseau/ostep-homework/),[CheatSheet](https://github.com/xxyzz/ostep-hw),[MIT6.826](https://pdos.csail.mit.edu/6.828/2020/schedule.html)
## Intro
1. Dialogue
   I hear and I forget. I see and I remeber. I do and I understand.

2. Introduction to Operating Systems
    - Three Pieces: **Virtualization, Conccurent, Persisetnce**.
   
   **CRUX**: how to virutalize resources.
   
   **CRUX**: how to build correct concurrent programs.
   
   **CRUX**: how to store data persistenlty?
   
    - **Von Neumann**
    - CPU is sometimes know as a resource manager.
    - Some concepts: virtualization, API, System calls, Standard Library.
    - 每个进程有他们自己private的虚拟地址空间
    - OS is seen as a standard library.
    - write有讲究：1）先延迟一会儿按batch操作 2）protocol, such as  journaling or copy-on-write 3)复杂结构 B-trees
    - Design Goal：
      - performance: minimize the overheads
      - protection: isolation
      - reliability
      - security
      - mobility
      - energy-efficiency
    - History:
      - libraries
      - protection: system call 
      - trap handler, user/kernel mode: return-from-trap
      - multiprogramming minicomputer
      - memory protection concurrency, ASIDE: The Importance Of [UNIX](https://www.youtube.com/watch?v=tc4ROCJYbm0)
      - modern era: PC Linux Torvalds: Linux

## Virtulization
1. Dialogue
2. Processes
    - Defintion: a running program

  **CRUX**: How to provide the illusion of many CPUs?

  - time sharing of the CPU allows users to run as many concurrent processes as they would like
  - low-level machinery : **mechanisms**: low-level methods or protocols that implement a needed piece of functionality.
  - Policies arfe algorithm for making some kind of decision within the OS. 在很多os中，共同的设计是将policy和mechanism分开，使用模块化。
  - machine state: memory(指令和数据都在其中), registers: PC aka IP告诉我们下一条会执行的指令, stack pointer& frame pointer用来管理函数参数，局部变量和返回地址的栈
  - Process API：
    - Creat、Destory、Wait、Miscellaneous Control、Status
  - Process Creation
    - load lazily: paging and swapping
    - run-time stack; heap(malloc(),free()):for dynamically-allocated data
    - I/O setups: default file descriptors
  - Process States
    - Running, Ready, Blocked
      ![state-transitions](state-trans.png)
  - Data Structures: process list, PCB
    - xv6 process structure
      ```cpp
      // the registers xv6 will save and restore
      // to stop and subsequently restart a process
      struct context {
          int eip;
          int esp;
          int ebx;
          int ecx;
          int edx;
          int esi;
          int edi;
          int ebp;
      };
      // the different states a process can be in
      enum proc_state { UNUSED, EMBRYO, SLEEPING,RUNNABLE, RUNNING, ZOMBIE };
      // the information xv6 tracks about each process
      // including its register context and state
      struct proc {
          char *mem;                  // Start of process memory
          uint sz;                    // Size of process memory
          char *kstack;               // Bottom of kernel stack
                                    // for this process
          enum proc_state state;      // Process state
          int pid;                    // Process ID
          struct proc*parent;        // Parent process
          void *chan;                 // If !zero, sleeping on chan
          int killed;                 // If !zero, has been killed
          struct file *ofile[NOFILE]; // Open files
          struct inode *cwd;          // Current directory
          struct context context;     // Switch here to run process
          struct trapframe *tf;       // Trap frame for the
                                      // current interrupt
      };
      ```


