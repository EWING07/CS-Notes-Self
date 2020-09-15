## Lecture 1. Courese overview + the shell

- Shell: 空格分割输入，`~` is short for "home", `.`代表当前文件夹, `..`表示父目录。
  - `.`在UNIX系统的[遗留问题]("")
- environment variable: `echo $PATH`; `vim ~/.zshrc`
  - `$PATH`可以作为输入
- Connecting Programs:
  - `< file` and `> file` can rewrite the input and output streams of a program to a file
  - `>>` 可append 
  - `|` lets you "chain" programs such that the output of one is the input of another.
- [Shell中不同类型quotes的含义](https://www.gnu.org/software/bash/manual/html_node/Quoting.html) 

## Lecture 2 Shell Tools and Scripting

1. Shell scripting
   1. foo=bar, $foo注意等号前后不能有space,否则会被当作参数。space character will perform argument splitting.
   2. 单引号和双引号的区别：对于$foo来说，`'`是literal meaning, `"`会将变量替换成变量值。
   3. shell scripting 也有if, case, while, for, function特性
      1. source mcd.sh后即可以用。cd如果在function内部使用，针对的是子shell，不影响外部，因此直接使用哦那个./mcd.sh不合适
      ```shell
      #!/bin/bash
      mcd(){
            mkdir -p "$1"
            cd "$1"
      }
      ```
      2. [Special Characters in shell](https://tldp.org/LDP/abs/html/special-chars.html)
         1. `$0` - 脚本名
         2. `$1` 到 `$9` - 脚本到参数。 `$1` 是第一个参数，依此类推。
         3. `$@` - 所有参数
         4. `$#` - 参数个数
         5. `$?` - 前一个命令到返回值
         6. `$$` - 当前脚本到进程识别码
         7. `!!` - 完整到上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 sudo !!再尝试一次。
         8. `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式shell，你可以通过按下`Esc`之后键入 . 来获取这个值。
      3. [Linux中，&和&&, |和|| ,&> 与 >的区别](https://blog.csdn.net/sunfengye/article/details/78973831)
2. [Linux-Shell中国各种替换的辨析](https://www.cnblogs.com/chengd/p/7803664.html)
   1. 命令通常使用 `STDOUT`来返回输出值，使用`STDERR` 来返回错误及错误码，便于脚本以更加友好到方式报告错误。 返回码或退出状态是脚本/命令之间交流执行状态到方式。返回值0表示正常执行，其他所有非0的返回值都表示有错误发生。
   2. 退出码可以搭配`&&` (与操作符) 和`||` (或操作符)使用，用来进行条件判断，决定是否执行其他程序。同一行的多个命令可以用 ; 分隔。程序`true`的返回码永远是0，`false`的返回码永远是1。
   ```shell
    false || echo "Oops, fail"
    # Oops, fail
    true || echo "Will not be printed"
    #
    true && echo "Things went well"
    # Things went well
    false && echo "Will not be printed"
    #
    false ; echo "This will always run"
    # This will always run
   ```
   3. command substitution: `for file in <img src="https://www.zhihu.com/equation?tex=%28ls%29%60%EF%BC%8C%E5%8F%AF%E4%BB%A5%E7%94%A8%60%27%20%27%60%E4%BB%A3%E6%9B%BF%60" alt="(ls)，可以用' '代替" class="ee_img tr_noresize" eeimg="1"> ( )`，但后者辨识度更高
   4. process substitution: 生成返回temporary file，`diff <(ls foo) <(ls bar)`
    ``` shell
    #!/bin/bash
    echo "Starting program at $(date)" # Date will be substituted

    echo "Running program  <img src="https://www.zhihu.com/equation?tex=0%20with%20" alt="0 with " class="ee_img tr_noresize" eeimg="1"> # arguments with pid $$"

    for file in $@; do
        grep foobar $file > /dev/null 2> /dev/null
        # When pattern is not found, grep has exit status 1
        # We redirect STDOUT and STDERR to a null register since we do not care about them
        if [[ $? -ne 0 ]]; then
            echo "File $file does not have any foobar, adding one"
            echo "# foobar" >> "$file"
        fi
    done
    ```
    - 2>重定向stderr；引申：>&2，重定向到stderr
    - -ne，更多的查看man test or [test手册](https://man7.org/linux/man-pages/man1/test.1.html)
    - “test command”， [[[和[的区别](http://mywiki.wooledge.org/BashFAQ/031) ，[[是compound command，存在special parsing context，寻找reserved words or control operators
3. Shell Globbing
   1.  wildcard通配符：?和* ls *.sh
   2.  {}: mv *{.py,.sh} folder
   3.  touch {foo,bar}/{a..h}
   4.  利用[shellcheck](https://github.com/koalaman/shellcheck)检查shell scripts的错误
   5.  [shellbang](https://en.wikipedia.org/wiki/Shebang_(Unix))可以利用env进行解释，它会利用环境变量中的程序来解析该脚本，这样就提高来您的脚本的可移植性。
   6.  shell函数和脚本的不同点：
       1. Functions have to be in the same language as the shell, while scripts can be written in any language. This is why including a shebang for scripts is important.
       2. Functions are loaded once when their definition is read. Scripts are loaded every time they are executed. This makes functions slightly faster to load but whenever you change them you will have to reload their definition.
       3. Functions are executed in the current shell environment whereas scripts execute in their own process. Thus, functions can modify environment variables, e.g. change your current directory, whereas scripts can’t. Scripts will be passed by value environment variables that have been exported using export
          1. 比如cd只能在function中影响到外界shell
       4. As with any programming language functions are a powerful construct to achieve modularity, code reuse and clarity of shell code. Often shell scripts will include their own function definitions.
4. Shell Tools
   1. 帮助文档
      1. XX -h
      2. man XX
      3. :help 或 ? (interactive)
      4. [tldr](https://tldr.sh/)：比man好用！
   2. 查找工具
      1. 查找文件：find, fd, [locate](https://man7.org/linux/man-pages/man1/locate.1.html), 见底部命令解释
      2. 查找代码：[grep](https://man7.org/linux/man-pages/man1/grep.1.html), ack, ag and rg
         1. grep -R can be improved in many ways, such as ignoring .git folders, using multi CPU support, &c
         2. 代码行数统计工具cloc
         ```shell
         # 查找所有使用了 requests 库的文件
         rg -t py 'import requests'
         # 查找所有没有写 shebang 的文件（包含隐藏文件）
         rg -u --files-without-match "^#!"
         # 查找所有的foo字符串，并打印其之后的5行
         rg foo -A 5
         # 打印匹配的统计信息（匹配的行和文件的数量）
         rg --stats PATTERN
         ```
         
         重要的是你要知道有些问题使用合适的工具就会迎刃而解，而具体选择哪个工具则不是那么重要。
   3. 查找shell指令
      1. history | grep find
      2. Ctrl-r，可结合[fzf](https://www.jianshu.com/p/d64553a37d69)，[教程](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r)：高效查找，手动选择
      3. [zsh-history-substring-search](https://github.com/zsh-users/zsh-history-substring-search): 键盘上下键寻找历史
      4. [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)：键盘右键快速键入
      5. 如果输入命令有leading space，不会记入历史数据；如果不慎记入，可修改.bash_history或.zsh_history
   4. 查找目录
      1. [fasd](https://github.com/clvv/fasd) 用frecency(frequency+recency)这个指标排序，这一指标最早用于火狐浏览器
      2. [autojump](https://github.com/wting/autojump)
      3. More complex tools exist to quickly get an overview of a directory structure [tree](https://linux.die.net/man/1/tree), [broot](https://github.com/Canop/broot) or even full fledged file managers like [nnn](https://github.com/jarun/nnn) or [ranger](https://github.com/ranger/ranger)
5. Shell Edit 
   1. Ctrl-a光标移动到行前
   2. ESC进入Vim-mode，ESC-v进入Vim直接编辑
6. 练习
   1. ls -laht
   2. macro记录pwd，polo进入
      ```shell
      #!/bin/bash
      marco(){
            foo=$(pwd)
            export MARCO=$foo
      }
      polo(){
            cd "$MARCO" || echo "cd error"
      }
      ```
   3. 一直检测循环
      ```shell
      #!/usr/local/bin/zsh
      count=0
      while true; do
         ./test.sh &> res
         if [[ "$?" -ne 0 ]]; then
         break
         fi
         count=$[count+1]
      done
      echo "total run times: $count"
      cat res
      ```
   4. `find . -name "*.html" -print0 | xargs -0 zip -r html.zip`
   5. `ls -lt`


PS: 有不懂的就看man page
