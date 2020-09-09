# Lua Learning Notes

1. Lua数据类型
   - Lua中有8个基本类型分别为：**nil, boolean, number, string, userdata, function, thread和table**
   - function是由C或Lua编写的类型，userdata表示任意存储在变量中的C数据结构，thread表示执行的独立线路，用于执行协同程序
   - table是一个关联数组，数组的索引可以是数字、字符串或表类型。在Lua里，table的创建是通过“构造表达式“来完成，最简单的构造表达式是{}，用来创建一个空表。
   - 对于全局变量和table，nil又一个删除作用，给全局变量或者table表里的变量赋一个nil值，等同于把它们删掉。
   - string由一对双引号或单引号来表示。字符串连接使用的是`..`，`#`来计算字符串的长度，放在字符串前面，例如：`len = "www.baidu.com" #len`
   - 不同于其他语言的数组把 0 作为数组的初始索引，在 Lua 里表的默认初始索引一般以 1 开始。
   - function在lua中被看作是第一类值，函数可以存在变量里。还可以以匿名函数的方式通过参数传递

2. Lua变量
   1. 有三种类型：全局变量、局部变量、表中的域。用`local`显示声明局部变量，变量的默认值均为nil
   2. Lua 可以对多个变量同时赋值，变量列表和值列表的各个元素用逗号分开，赋值语句右边的值会依次赋给左边的变量。
        - 尽可能使用局部变量，有两个好处：1) 避免命名冲突   2) 访问局部变量比全局变量快
        - 对 table 的索引使用方括号 []。Lua 也提供了 . 操作。eg: `map.key` 或 `map["key"]`

3. Lua循环
   1. while循环:
        
        ```Lua
        while(condition)
        do
            statements
        end
        ```

    2. Lua 编程语言中 for 循环语句可以重复执行指定语句，重复次数可在 for 语句中控制。
       1. 数值for循环
        
        ```lua
        for var=exp1,exp2,exp3 do
            <statements>
        end
        ```
        var从 exp1 变化到 exp2，每次变化以 exp3 为步长递增 var，并执行一次 **"执行体"**。exp3 是可选的，如果不指定，默认为1。

        2. 泛型for循环
        
        泛型 for 循环通过一个迭代器函数来遍历所有值，类似 java 中的 foreach 语句。
        ```lua
        a = {"one", "two", "three"}
        for i, v in ipairs(a) do
        print(i, v)
        end 
        ```
        i是数组索引值，v是对应索引的数组元素值。ipairs是Lua提供的一个迭代器函数，用来迭代数组。
    3. repeat...until循环(类似do...while)
        ```lua
        repeat
            statements
        until(condition)
        ```
    4. break语句：退出当前循环或语句，并开始脚本执行紧接着的语句
    5. goto语句
        Lua 语言中的 goto 语句允许将控制流程无条件地转到被标记的语句处，语法格式如下：
        `goto Label`
        Label的格式为：
        `::Label::`
        
        可以实现continue的功能：
        ```lua
        for i=1, 3 do
            if i <= 2 then
                print(i, "yes continue")
                goto continue
            end
            print(i, " no continue")
            ::continue::
            print([[i'm end]])
        end
        ```
4. Lua流程控制
   1. 控制结构的条件表达式结果可以是任何值，Lua认为false和nil为假，true和非nil为真。需要注意的是Lua中0为true：
    ```lua
    if( 布尔表达式 1)
    then
        --[ 在布尔表达式 1 为 true 时执行该语句块 --]

    elseif( 布尔表达式 2)
    then
        --[ 在布尔表达式 2 为 true 时执行该语句块 --]

    elseif( 布尔表达式 3)
    then
        --[ 在布尔表达式 3 为 true 时执行该语句块 --]
    else 
        --[ 如果以上布尔表达式都不为 true 则执行该语句块 --]
    end
    ```
5. Lua函数
   1. Lua函数定义格式如下:
   ```lua
    optional_function_scope function function_name( argument1, argument2, argument3..., argumentn)
        function_body
        return result_params_comma_separated
    end
   ```
    - **optional_function_scope**: 该参数是可选的制定函数是全局函数还是局部函数，未设置该参数默认为全局函数，如果你需要设置函数为局部函数需要使用关键字 **local**。
    - **function_name**: 指定函数名称。
    - **argument1**, **argument2**, **argument3...**, **argumentn**: 函数参数，多个参数以逗号隔开，函数也可以不带参数。
    - **function_body**: 函数体，函数中需要执行的代码语句块。
    - **result_params_comma_separated**: 函数返回值，Lua语言函数可以返回多个值，每个值以逗号隔开。
   2. 多返回值:Lua函数可以返回多个结果值，比如string.find，其返回匹配串"开始和结束的下标"（如果不存在匹配串返回nil）。
   3. 可变参数：Lua 函数可以接受可变数目的参数，和 C 语言类似，在函数参数列表中使用三点 ... 表示函数有可变的参数。
   4. 也可以通过 select("#",...) 来获取可变参数的数量, 调用select时，必须传入一个固定实参selector(选择开关)和一系列变长参数。如果selector为数字n,那么select返回它的第n个可变实参，否则只能为字符串"#",这样select会返回变长参数的总数.
6. Lua运算符：
   1. `~=`表示不等于：在使用“==”做等于判断时，要注意对于 table, userdate 和函数， Lua 是作引用比较的。也就是说，只有当两个变量引用同一个对象时，才认为它们相等。
   2. Lua 中的 and 和 or 是不同于 c 语言的。在 c 语言中，and 和 or 只得到两个值 1 和 0，其中 1 表示真，0 表示假。而 Lua 中 and 的执行过程是这样的：
      1. a and b 如果 a 为 nil，则返回 a，否则返回 b;
      2. a or b 如果 a 为 nil，则返回 b，否则返回 a。
7. Lua字符串
   1. [字符串操作](https://www.runoob.com/lua/lua-strings.html)
   2. 字符串格式化
8. Lua数组
   1. 默认索引值从1开始，但是也可以指定其他数值，例如：0或者负数
9.  Lua迭代器
   2.  **迭代器（iterator）**是一种对象，它能够用来遍历标准模板库容器中的部分或全部元素，每个迭代器对象代表容器中的确定的地址。在 Lua 中迭代器是一种支持指针类型的结构，它可以遍历集合的每一个元素。
   3.  **无状态的迭代器：**无状态的迭代器是指不保留任何状态的迭代器，因此在循环中我们可以利用无状态迭代器避免创建闭包花费额外的代价。每一次迭代，迭代函数都是用两个变量（状态常量和控制变量）的值作为参数被调用，一个无状态的迭代器只利用这两个值可以获取下一个元素。
   4.  **多状态的迭代器：**很多情况下，迭代器需要保存多个状态信息而不是简单的状态常量和控制变量，最简单的方法是使用闭包，还有一种方法就是将所有的状态信息封装到 table 内，将 table 作为迭代器的状态常量，因为这种情况下可以将所有的信息存放在 table 内，所以迭代函数通常不需要第二个参数。
10. Lua表（table）
    1.  当我们为 table a 并设置元素，然后将 a 赋值给 b，则 a 与 b 都指向同一个内存。如果 a 设置为 nil ，则 b 同样能访问 table 的元素。如果没有指定的变量指向a，Lua的垃圾回收机制会清理相对应的内存。
    2.  [table操作](https://www.runoob.com/lua/lua-tables.html)
11. Lua模块与包
    1.  Lua 的模块是由变量、函数等已知元素组成的 table，因此创建一个模块很简单，就是创建一个 table，然后把需要导出的常量、函数放入其中，最后返回这个 table 就行。以下为创建自定义模块 module.lua，文件代码格式如下：
    ```lua
    -- 文件名为 module.lua
    -- 定义一个名为 module 的模块
    module = {}
    
    -- 定义一个常量
    module.constant = "这是一个常量"
    
    -- 定义一个函数
    function module.func1()
        io.write("这是一个公有函数！\n")
    end
    
    local function func2()
        print("这是一个私有函数！")
    end
    
    function module.func3()
        func2()
    end
    
    return module
    ```
    需要使用require函数来加载模块，只修腰简单地调用就可以了。例如：`require("module_name")` 或者`require "module_name"`, 执行 require 后会返回一个由模块常量或函数组成的 table，并且还会定义一个包含该 table 的全局变量。
    1. 如果找过目标文件，则会调用 package.loadfile 来加载模块。否则，就会去找 C 程序库。搜索的文件路径是从全局变量 package.cpath 获取，而这个变量则是通过环境变量 LUA_CPATH 来初始。搜索的策略跟上面的一样，只不过现在换成搜索的是 so 或 dll 类型的文件。如果找得到，那么 require 就会通过 package.loadlib 来加载.
    2. C包：与Lua中写包不同，C包在使用以前必须首先加载并连接，在大多数系统中最容易的实现方式是通过动态连接库机制。Lua在一个叫loadlib的函数内提供了所有的动态连接的功能。这个函数有两个参数:库的绝对路径和初始化函数。loadlib 函数加载指定的库并且连接到 Lua，然而它并不打开库（也就是说没有调用初始化函数），反之他返回初始化函数作为 Lua 的一个函数，这样我们就可以直接在Lua中调用他。如果加载动态库或者查找初始化函数时出错，loadlib 将返回 nil 和错误信息。我们可以修改前面一段代码，使其检测错误然后调用初始化函数：
    ```lua
    local path = "/usr/local/lua/lib/libluasocket.so"
    -- 或者 path = "C:\\windows\\luasocket.dll"，这是 Window 平台下
    local f = assert(loadlib(path, "luaopen_socket"))
    f()  -- 真正打开库
    ```
12. [Lua元表(metatable)](https://www.runoob.com/lua/lua-metatables.html)
13. Lua协同程序(coroutine)
    1.  线程与协同程序的主要区别在于，一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行。在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起。协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同。
    2.  [基本语法](https://www.runoob.com/lua/lua-coroutine.html)
    3.  [使用样例](https://www.runoob.com/lua/lua-coroutine.html)
14. [Lua文件I/O](https://www.runoob.com/lua/lua-file-io.html)
15. [Lua错误处理](https://www.runoob.com/lua/lua-error-handling.html):assert函数和error函数
16. [Lua调试](https://www.runoob.com/lua/lua-debug.html)
17. [Lua垃圾回收](https://www.runoob.com/lua/lua-garbage-collection.html)
18. [Lua面向对象](https://www.runoob.com/lua/lua-object-oriented.html)
    Lua基本结构是table，所以需要用table来描述对象的属性
19. [Lua数据库访问](https://www.runoob.com/lua/lua-database-access.html)
20. LuaJIT
    LuaJIT 是采用 C 和汇编语言编写的 Lua 解释器与即时编译器。LuaJIT 被设计成全兼容标准的 Lua 5.1 语言，同时可选地支持 Lua 5.2 和 Lua 5.3 中的一些不破坏向后兼容性的有用特性。因此，标准 Lua 语言的代码可以不加修改地运行在 LuaJIT 之上。LuaJIT 和标准 Lua 解释器的一大区别是，LuaJIT 的执行速度，即使是其汇编编写的 Lua 解释器，也要比标准 Lua 5.1 解释器快很多，可以说是一个高效的 Lua 实现。另一个区别是，LuaJIT 支持比标准 Lua 5.1 语言更多的基本原语和特性，因此功能上也要更加强大。