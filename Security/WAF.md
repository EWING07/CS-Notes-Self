# WAF安全应用防火墙

## 了解WAF

1. 定义

    Web应用防护系统（Web Application Firewall,简称WAF),是通过执行一系列针对http/https的安全策略来专门为web应用提供保护的一款产品

2. WAF的功能
   1. 支持IP白名单和黑名单，直接将黑名单的IP访问拒绝。
   2. 支持URL白名单，将不需要过滤的URL进行定义。
   3. 支持User-Agent的过滤，匹配自定义规则中的条目，然后进行处理（返回403）。
   4. 支持[CC攻击](https://www.jianshu.com/p/dff5a0d537d8)防护，单个URL指定时间的访问次数，超过设定值，直接返回403。
   5. 支持Cookie过滤，匹配自定义规则中的条目，然后进行处理（返回403）。
   6. 支持URL过滤，匹配自定义规则中的条目，如果用户请求的URL包含这些，返回403。
   7. 支持URL参数过滤，原理同上。
   8. 支持日志记录，将所有拒绝的操作，记录到日志中去

3. WAF的特点
   1. 异常检测
        Web应用防火墙会对HTTP的请求进行异常检测，拒绝不符合HTTP标准的请求。并且，它也可以只允许HTTP协议的部分选项通过，从而减少攻击的影响范围。甚至，一些Web应用防火墙还可以严格限定HTTP协议中那些过于松散或未被完全制定的选项。