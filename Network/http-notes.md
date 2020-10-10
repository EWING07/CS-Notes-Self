# Notes for HTTP

> Reference: 《图解HTTP》

## Chapter 1 了解Web以网络基础

### 1.1 使用HTTP协议访问Web

大致过程：根据web浏览器中的URL，web浏览器从web服务器端获取resource等信息，从而显示出web页面。

发出请求获取服务器资源的web浏览器等，可以称为客户端。

而web使用HTTP(hyperText Transfer Protocol)，来进行客户端到服务端等一系列运作流程。而协议是指规则的约定，可以说，**web是建立在http协议上通信的。**

### 1.2 HTTP的诞生

当年HTTP的出现主要是为了解决文本传输的难题。由于协议本身本身简单，于此基础上已经应用到很多其他的场景中了。

目前我们用的大多是HTTP1.1，HTTP2.0在制定当中，但是普及可能还需要时间。

### 1.3 网络基础TCP/IP

