# Core Java 1 Learning Notes

## Chapter 1 Java程序设计概述
白皮书关键术语：简单性、面向对象、分布式、健壮性、安全性、体系结构中立、可移植性、解释型、高性能、多线程、动态性

## Chapter 2 Java程序设计环境
JDK-Java Deleopment Kit，JRE-Java Runtime Environment

## Chapter 3 Java的基本程序设计结构

1. 一般使用int，byte和short类型主要用于特定场合，例如，底层的文件处理或者需要控制占用存储空间的大数组。
2. 长整型数值有一个后缀L或l。十六进制数值有一个前缀0x或者0X。八进制有个前缀0。
3. java7开始，加上前缀0b或0B就可以写二进制数，还可以为数字字面量加下划线。
4. java中，所有的数值类型所占据的字节数量与平台无关，这一点与C/C++不同，这样对编写跨平台程序带来了方便。Java没有任何无符号形式的int、long、short或byte类型。
5. 对于浮点类型，float类型数值有一个后缀F或f。没有后缀F的浮点数值默认为double类型（当然也可以在后面加D或d）PS：可以使用十六进制表示浮点数值，例如：0.125=2<sup>-3</sup>可以表示成0x1.0p-3。p表示指数，不是e。
6. 三个特殊的浮点数：Double.POSITIVE_INFINITY, Double.NEGATIVE_INFINITY和Double.NaN。用Double.isNaN(x)来判断是否为NaN。
7. final表示常量，习惯上用全大写。
8. 要检测两个字符串是否相等，需要使用equals，一定不要使用==运算符，因为这个运算符只能确定两个字符串是否放置在同一个位置上。如果虚拟机始终将相同的字符串共享，就可以使用==运算符检测是否相等。但实际上只有字符串常量是共享的，而+或substring等操作产生的结果并不是共享的。
9. Java没有提供运算符重载功能，比如载大数值中需要用其类中的add和multiply方法。
10. 数组拷贝使用Arrays.copyOf(luckyNumber, 2*luckyNumbers.length);
11. java应用程序的main方法中，程序名并没有存储载args数组中。