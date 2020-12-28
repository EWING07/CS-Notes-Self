# MIT 6.828 OS Lab

> Reference: [参考](https://zhuanlan.zhihu.com/p/74028717?utm_source=wechat_session&utm_medium=social&utm_oi=619085716488065024&utm_content=sec)

## Lab 1: Booting a PC

### Part 1: PC Bootstrap

BIOS的操作就是在控制，初始化，检测各种底层的设备，比如时钟，GDTR寄存器。以及设置中断向量表。作为PC启动后运行的第一段程序，**它最重要的功能是把操作系统从磁盘中导入内存，然后再把控制权转交给操作系统。**所以BIOS在运行的最后会去检测可以从当前系统的哪个设备中找到操作系统，通常来说是我们的磁盘。也有可能是U盘等等。当BIOS确定了，操作系统位于磁盘中，那么它就会把这个磁盘的第一个扇区，通常把它叫做启动区（boot sector）先加载到内存中，这个启动区中包括一个非常重要的程序--**boot loader，**它会负责完成整个操作系统从磁盘导入内存的工作，以及一些其他的非常重要的配置工作。最后操作系统才会开始运行。

 可见PC启动后的运行顺序为 BIOS --> boot loader --> 操作系统内核

