# OpenResty Best Practice Notes

[OpenResty Best Practice GitBook](https://moonbingbing.gitbooks.io/openresty-best-practices/content/), [curl命令大全](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)

## 1. Socket编程发展
   Linux Socket 编程领域，为了处理大量连接请求场景，需要使用非阻塞 I/O 和复用。select、poll 和 epoll 是 Linux API 提供的 I/O 复用方式，自从 Linux 2.6 中加入了 epoll 之后，高性能服务器领域得到广泛的应用，现在比较出名的 Nginx 就是使用 epoll 来实现 I/O 复用支持高并发，目前在高并发的场景下，Nginx 越来越受到欢迎。
###   1. Select模型
- Select函数接口：
        ```c
            int select (int n, fd_set *readfds, fd_set *writefds,
                fd_set *exceptfds, struct timeval *timeout);
        ```
        select 函数监视的文件描述符分 3 类，分别是 writefds、readfds 和 exceptfds。调用后 select 函数会阻塞，直到有描述符就绪（有数据 可读、可写、或者有 except），或者超时（timeout 指定等待时间，如果立即返回设为 null 即可）。当 select 函数返回后，通过遍历 fd_set，来找到就绪的描述符。

        select 目前几乎在所有的平台上支持，其良好跨平台支持是它的一大优点。select 的一个缺点在于单个进程能够监视的文件描述符的数量存在最大限制，在 Linux 上一般为 1024，可以通过修改宏定义甚至重新编译内核的方式提升这一限制，但是这样也会造成效率的降低。  
###   2. Poll模型
  - Poll函数接口：
        ```c
        int poll (struct pollfd *fds, unsigned int nfds, int timeout);
        ```
        不同于 select 使用三个位图来表示三个 fdset 的方式，poll 使用一个 pollfd 的指针实现。
        ```c
        struct pollfd {
            int fd; /* file descriptor */
            short events; /* requested events to watch */
            short revents; /* returned events witnessed */
        };
        ```
        pollfd 结构包含了要监视的 event 和发生的 event，不再使用 select “参数-值”传递的方式。同时，pollfd 并没有最大数量限制（但是数量过大后性能也是会下降）。和 select 函数一样，poll 返回后，需要轮询 pollfd 来获取就绪的描述符。

        从上面看，select 和 poll 都需要在返回后，通过遍历文件描述符来获取已经就绪的 socket。事实上，同时连接的大量客户端在一时刻可能只有很少的处于就绪状态，因此随着监视的描述符数量的增长，其效率也会线性下降。
###   3. Epoll模型
  - Epoll的接口如下：
        ```c
        int epoll_create(int size)；
        int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；
            typedef union epoll_data {
                void *ptr;
                int fd;
                __uint32_t u32;
                __uint64_t u64;
            } epoll_data_t;

            struct epoll_event {
                __uint32_t events;      /* Epoll events */
                epoll_data_t data;      /* User data variable */
            };

            int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
        ```
      - epoll_creare函数创建epoll文件描述符，参数size并不是限制epoll所能监听的描述符最大个数，只是对内核初始分配内部数据结构的一个建议。
      - epoll_ctl完成对指定描述符fd执行op操作控制，event是与fd关联的监听事件。op操作有三种：添加EPOLL_CTL_ADD，删除EPOLL_CTL_DEL，修改EPOLL_CTL_MOD。分别添加、删除和修改对fd的监听事件。
      - epoll_wait等待epfd上的IO事件，最多返回maxevents个事件。
      - 在 select/poll 中，进程只有在调用一定的方法后，内核才对所有监视的文件描述符进行扫描，而 epoll 事先通过 epoll_ctl() 来注册一个文件描述符，一旦基于某个文件描述符就绪时，内核会采用类似 callback 的回调机制，迅速激活这个文件描述符，当进程调用 epoll_wait 时便得到通知。
    - epoll 的优点主要是一下几个方面：
        1. 监视的描述符数量不受限制，它所支持的 fd 上限是最大可以打开文件的数目，这个数字一般远大于 2048, 举个例子, 在 1GB 内存的机器上大约是 10 万左右，具体数目可以 cat /proc/sys/fs/file-max 察看, 一般来说这个数目和系统内存关系很大。select 的最大缺点就是进程打开的 fd 是有数量限制的。这对于连接数量比较大的服务器来说根本不能满足。虽然也可以选择多进程的解决方案( Apache 就是这样实现的)，不过虽然 linux 上面创建进程的代价比较小，但仍旧是不可忽视的，加上进程间数据同步远比不上线程间同步的高效，所以也不是一种完美的方案。
        2. 监视的描述符数量不受限制，它所支持的 fd 上限是最大可以打开文件的数目，这个数字一般远大于 2048, 举个例子, 在 1GB 内存的机器上大约是 10 万左右，具体数目可以 cat /proc/sys/fs/file-max 察看, 一般来说这个数目和系统内存关系很大。select 的最大缺点就是进程打开的 fd 是有数量限制的。这对于连接数量比较大的服务器来说根本不能满足。虽然也可以选择多进程的解决方案( Apache 就是这样实现的)，不过虽然 linux 上面创建进程的代价比较小，但仍旧是不可忽视的，加上进程间数据同步远比不上线程间同步的高效，所以也不是一种完美的方案。 
        3. 支持水平触发和边沿触发两种模式：
            1. 水平触发模式，文件描述符状态发生变化后，如果没有采取行动，它将后面反复通知，这种情况下编程相对简单，libevent 等开源库很多都是使用的这种模式。
            2. 边沿触发模式，只告诉进程哪些文件描述符刚刚变为就绪状态，只说一遍，如果没有采取行动，那么它将不会再次告知。理论上边缘触发的性能要更高一些，但是代码实现相当复杂（Nginx 使用的边缘触发）。
        4. mmap 加速内核与用户空间的信息传递。epoll 是通过内核与用户空间 mmap 同一块内存，避免了无谓的内存拷贝。
## 2. OpenResty简介
    OpenResty（也称为 ngx_openresty）是一个全功能的 Web 应用服务器。它打包了标准的 Nginx 核心，很多的常用的第三方模块，以及它们的大多数依赖项。
  1. 与其他location配合
    - 内部调用
        例如对数据库、内部公共函数的统一接口，可以把它们放到统一的 location 中。通常情况下，为了保护这些内部接口，都会把这些接口设置为 internal 。这么做的最主要好处就是可以让这个内部接口相对独立，不受外界干扰。
        
        该方法，可以被广泛应用于广告系统（1：N模型，一个请求，后端从N家供应商中获取条件最优广告）、高并发前端页面展示（并行无依赖界面、降级开关等）。
    - 流水线方式跳转
        
        现在的网络请求，已经变得越来越拥挤。各种不同 API 、下载请求混杂在一起，就要求不同厂商对下载的动态调整有各种不同的定制策略，而这些策略在一天的不同时间段，规则可能还不一样。这时候我们还可以效仿工厂的流水线模式，逐层过滤、处理。
        
        示例代码：
        ```conf
        location ~ ^/static/([-_a-zA-Z0-9/]+).jpg {
            set $image_name $1;
            content_by_lua_block {
                ngx.exec("/download_internal/images/"
                        .. ngx.var.image_name .. ".jpg");
            };
        }

        location /download_internal {
            internal;
            # 这里还可以有其他统一的 download 下载设置，例如限速等
            alias ../download;
        }
        ```
        注意，ngx.exec 方法与 ngx.redirect 是完全不同的，前者是个纯粹的内部跳转并且没有引入任何额外 HTTP 信号。 这里的两个 location 更像是流水线上工人之间的协作关系。第一环节的工人对完成自己处理部分后，直接交给第二环节处理人（实际上可以有更多环节），它们之间的数据流是定向的。
    - 外部重定向
        不知道大家什么时候开始注意的，百度的首页已经不再是 HTTP 协议，它已经全面修改到了 HTTPS 协议上。但是对于大家的输入习惯，估计还是在地址栏里面输入 baidu.com ，回车后发现它会自动跳转到 https://www.baidu.com ，这时候就需要的外部重定向了。

        与之前两个应用实例不同的，外部重定向是可以跨域名的。例如从 A 网站跳转到 B 网站是绝对允许的。在 CDN 场景的大量下载应用中，一般分为调度、存储两个重要环节。调度就是通过根据请求方 IP 、下载文件等信息寻找最近、最快节点，应答跳转给请求方完成下载。
  2. 获取uri参数
    - 获取请求uri参数
        首先看一下官方 API 文档，获取一个 uri 有两个方法：`ngx.req.get_uri_args`、`ngx.req.get_post_args`，二者主要的区别是参数来源有区别。
        参考下面例子：
        ```conf
        server {
            listen    80;
            server_name  localhost;

            location /print_param {
                content_by_lua_block {
                    local arg = ngx.req.get_uri_args()
                    for k,v in pairs(arg) do
                        ngx.say("[GET ] key:", k, " v:", v)
                    end

                    ngx.req.read_body() -- 解析 body 参数之前一定要先读取 body
                    local arg = ngx.req.get_post_args()
                    for k,v in pairs(arg) do
                        ngx.say("[POST] key:", k, " v:", v)
                    end
                }
            }
        }
        ```
        输出结果：
        ```terminal
        ➜  ~  curl '127.0.0.1/print_param?a=1&b=2%26' -d 'c=3&d=4%26'
            [GET ] key:b v:2&
            [GET ] key:a v:1
            [POST] key:d v:4&
            [POST] key:c v:3
        ```
        从这个例子中，我们可以很明显看到两个函数 `ngx.req.get_uri_args`、`ngx.req.get_post_args` 获取数据来源是有明显区别的，前者来自 uri 请求参数，而后者来自 post 请求内容。
  3. 获取请求body 
    在 Nginx 的典型应用场景中，几乎都是只读取 HTTP 头即可，例如负载均衡、正反向代理等场景。但是对于 API Server 或者 Web Application ，对 body 可以说就比较敏感了。由于 OpenResty 基于 Nginx ，所以天然的对请求 body 的读取细节与其他成熟 Web 框架有些不同。

  4. 输出响应体
    - HTTP响应报文分为三个部分：1）响应行   2）响应头   3）响应体
    ![http响应报文](http_response_protocal.jpg)
    - 对于 HTTP 响应体的输出，在 OpenResty 中调用`ngx.say`或`ngx.print`即可。经过查看官方 wiki ，这两者都是输出响应体，区别是`ngx.say`会对输出响应体多输出一个`\n`。如果你用的是浏览器完成的功能调试，使用这两着是没有区别的。但是如果使用各种终端工具，这时候使用`ngx.say`明显就更方便了。
    - `ngx.say`和`ngx.print`是异步输出，也就是说当调用`ngx.say`后并不会立刻输出响应体
    - 如何优雅处理响应体过大的输出
      - 如果响应体比较小，这时候相对就比较随意。但是如果响应体过大（例如超过 2G），是不能直接调用 API 完成响应体输出的。响应体过大，分两种情况：
        - 输出内容本身体积很大，例如超过 2G 的文件下载（要利用 HTTP 1.1 特性 CHUNKED 编码来完成，把一个大的响应体拆分成多个小的应答体，分批、有节制的响应给请求方）
        - 输出内容本身是由各种碎片拼凑的，碎片数量庞大，例如应答数据是某地区所有人的姓名（其实就是要利用 ngx.print 的特性了，它的输入参数可以是单个或多个字符串参数，也可以是 table 对象。也就是说当有非常多碎片数据时，没有必要一定连接成字符串后再进行输出。完全可以直接存放在 table 中，用数组的方式把这些碎片数据统一起来，直接调用 ngx.print(table) 即可。这种方式效率更高，并且更容易被优化。）
  5. 日志输出
    - 标准日志输出
        OpenResty 的标准日志输出原句为`ngx.log(log_level, ...)`，几乎可以在任何 ngx_lua 阶段进行日志的输出。

        大家可以在单行日志中获取很多有用的信息，例如：时间、日志级别、请求ID、错误代码位置、内容、客户端 IP 、请求参数等等，这些信息都是环境信息，可以用来辅助完成更多其他操作。当然我们也可以根据自己需要定义日志格式。

        日志输出级别使用的 error，只有等于或大于这个级别的日志才会输出。

        有关 Nginx 的日志级别，请看下表：
        ```nginx
        ngx.STDERR     -- 标准输出
        ngx.EMERG      -- 紧急报错
        ngx.ALERT      -- 报警
        ngx.CRIT       -- 严重，系统故障，触发运维告警系统
        ngx.ERR        -- 错误，业务不可恢复性错误
        ngx.WARN       -- 告警，业务中可忽略错误
        ngx.NOTICE     -- 提醒，业务比较重要信息
        ngx.INFO       -- 信息，业务琐碎日志信息，包含不同情况判断等
        ngx.DEBUG      -- 调试
        ```

        他们是一些常量，越往上等级越高。读者朋友可以尝试把 error log 日志级别修改为 info，然后重新执行一下测试用例，就可以看到全部日志输出结果了。

        对于应用开发，一般使用 ngx.INFO 到 ngx.CRIT 就够了。生产中错误日志开启到 error 级别就够了。如何正确使用这些级别呢？可能不同的人、不同的公司可能有不同见解。
    - 网络日志输出
        如果你的日志需要归集，并且对时效性要求比较高那么这里要推荐的库可能就让你很喜欢了。 lua-resty-logger-socket ，可以说很好的解决了上面提及的几个特性。

        lua-resty-logger-socket 的目标是替代 Nginx 标准的 ngx_http_log_module 以非阻塞 IO 方式推送 access log 到远程服务器上。对远程服务器的要求是支持 syslog-ng 的日志服务。
  6. 简单API Server框架
    - 测试成功（加减乘除，参考log/）
  7. 使用nginx内置绑定变量
  8. 子查询
    - Nginx 子请求是一种非常强有力的方式，它可以发起非阻塞的内部请求访问目标 location。目标 location 可以是配置文件中其他文件目录，或 任何 其他 nginx C 模块，包括 `ngx_proxy`、`ngx_fastcgi`、`ngx_memc`、`ngx_postgres`、`ngx_drizzle`，甚至 `ngx_lua` 自身等等 。
  9. 不同阶段共享变量