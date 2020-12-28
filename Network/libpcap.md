# libpcap

Unix/Linux平台下的网络数据包捕获函数库。是一个独立于系统的用户层包捕获接口，为底层网络检测提供了一个可移植的框架。著名的TCPDUMP就是在此基础上开发而成的。

## 工作原理

主要有由两部分组成：网络分接口（Network Tap）和数据过滤器（Packet Filter）

network tap从网络设备驱动程序中手机数据拷贝，过滤器决定是否接收该数据包。Libpcap利用BSD Packet Filter(BPF)算法对网卡接收到的链路层数据包进行过滤。**BPF算法的基本思想是在有BPF监听的网络中，网卡驱动将接收到的数据包复制一份交给BPF过滤器，过滤器根据用户定义的规则决定是否接收此数据包以及需要拷贝该数据包的那些内容，然后将过滤后的数据给与过滤器相关联的上层应用程序。**如果没有定义规则，则把全部数据交给上层应用程序。

![libpcap](https://img-blog.csdn.net/20171207163409555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHRtb3podQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

面向底层包捕获、面向中间层的数据包过滤、面向应用层的用户接口

libpcap的包捕获机制就是在数据链路层加一个旁路处理。当一个数据包到达网络接口时，libpcap首先利用链路层PF_PACKET原始套接字从链路层驱动程序中获得该数据包的拷贝，再通过Tap函数将数据包发给BPF过滤器。BPF过滤器根据用户已经定义好的过滤规则对数据包进行逐一匹配，匹配成功则放入内核缓冲区，并传递给用户缓冲区，匹配失败则直接丢弃。如果没有设置过滤规则，所有数据包都将放入内核缓冲区，并传递给用户层缓冲区。所以整个过程并不干扰系统自身的网路协议栈的处理。

## 使用流程

1. 首先决定对哪一个接口进行嗅探，比如eth0，我们可以用一个字符串来定义这个设备
2. 初始化pcap，使用的文件句柄传入需要嗅探的设备。同时支持多个设备的嗅探
3. 设置BPF。创建一个规则集合，编译并且使用它。这个过程分为三个阶段: 
   1. 规则集合被置于一个字符串内，并且被转换成能被pcap读的格式。
   2. 编译该规则（就是调用一个不被外部程序使用的函数）。
   3. 告诉pcap使用它来过滤数据包。
4. pcap进入它的主循环。在这个阶段内pcap一直工作到它接收了所有我们想要的包为止。每当它收到一个包（或者多个数据包）就调用另一个已经定义好的函数，这个函数可以做我们想要的任何工作，比如它可以剖析包的上层协议信息并给用户打印出结果，它可以将结果保存为一个文件，或者什么也不作。
5. 在嗅探到所需的数据后，我们要关闭会话并结束。

## 抓包框架

pcap_lookupdev()：函数用于查找网络设备，返回可被 pcap_open_live() 函数调用的网络设备名指针。

pcap_lookupnet()：函数获得指定网络设备的网络号和掩码。
pcap_open_live()： 函数用于打开网络设备，并且返回用于捕获网络数据包的数据包捕获描述字。对于此网络设备的操作都要基于此网络设备描述字。
pcap_compile()： 函数用于将用户制定的过滤策略编译到过滤程序中。
pcap_setfilter()：函数用于设置过滤器。
pcap_loop()：函数 pcap_dispatch() 函数用于捕获数据包，捕获后还可以进行处理，此外 pcap_next() 和 pcap_next_ex() 两个函数也可以用来捕获数据包。
pcap_close()：函数用于关闭网络设备，释放资源。