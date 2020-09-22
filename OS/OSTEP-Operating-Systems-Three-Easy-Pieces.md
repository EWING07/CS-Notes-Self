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

  - HW: run process-run.py
  - `-i IO_RUN_IMMIDIATE`发生IO的进程接下来会有IO的概率大，因此这样比较高效。
3. Interlude: Process API
   - CRUX: How to Create and Control Process?
   - fork() syscall is to create a new process
   - wait() syscall won't return until the child has run and exited
   - exec() syscall is useful when u want to run a program that is different from the calling program.
   - the separation of fork() and exec() allows the shell to do a whole bunch of useful things rather easily.
   - control-c: SIGINT(interrupt)
   - ps: 可以看到在运行的进程
   - top: 展示系统的进程而且他们占用了多少CPU和其他资源
   - superuser可以控制所以的进程
   - HW：
     - [exec族函数的作用](https://blog.csdn.net/u014530704/article/details/73848573?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.channel_param)
       - 我们用fork函数创建新进程后，经常会在新进程中调用exec函数去执行另外一个程序。当进程调用exec函数时，该进程被完全替换为新程序。因为调用exec函数并不创建新进程，所以前后进程的ID并没有改变。
       - 在调用进程内部执行一个可执行文件。可执行文件既可以是二进制文件，也可以是任何Linux下可执行的脚本文件。
       - exec函数族分别是：execl, execlp, execle, execv, execvp, execvpe
       - 函数原型：

          ```c
          #include <unistd.h>
          extern char **environ;

          int execl(const char *path, const char *arg, ...);
          int execlp(const char *file, const char *arg, ...);
          int execle(const char *path, const char *arg,..., char * const envp[]);
          int execv(const char *path, char *const argv[]);
          int execvp(const char *file, char *const argv[]);
          int execvpe(const char *file, char *const argv[],char *const envp[]);
          ```

       - 返回值：exec函数族的函数执行成功后不会返回，调用失败时，会设置errno并返回-1，然后从原程序的调用点接着往下执行
       - 参数说明：
         - path：可执行文件的路径名字
         - arg：可执行程序所带的参数，第一个参数为可执行文件名字，没有带路径且arg必须以NULL结束
         - file：如果参数file中包含/，则就将其视为路径名，否则就按 PATH环境变量，在它所指定的各目录中搜寻可执行文件。
         - exec族函数参数极难记忆和分辨，函数名中的字符会给我们一些帮助：
         - l : 使用参数列表
         - p：使用文件名，并从PATH环境进行寻找可执行文件
         - v：应先构造一个指向各参数的指针数组，然后将该数组的地址作为这些函数的参数。
         - e：多了envp[]数组，使用新的环境变量代替调用进程的环境变量
     - [waitpid函数详解](https://blog.csdn.net/Roland_Sun/article/details/32084825)
     - STDOUT_FILENO：向屏幕输出，STDIN_FILENO：接收键盘的输入
     - [read和write函数](https://www.cnblogs.com/xiehongfeng100/p/4619451.html)
     - [Linux进程间通信方式之pipe](https://blog.csdn.net/yangwen123/article/details/14118733?utm_medium=distribute.pc_relevant.none-task-blog-title-1&spm=1001.2101.3001.4242)