# Operating Systems Three Easy Pieces

### Topic: Virtulaization, Concurrency, Persistence
[Textbook](http://pages.cs.wisc.edu/~remzi/OSTEP/)
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

