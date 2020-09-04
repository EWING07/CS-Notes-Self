# Lecture 1. Courese overview + the shell

- Shell: 空格分割输入，`~` is short for "home", `.`代表当前文件夹, `..`表示父目录。
  - `.`在UNIX系统的[遗留问题]("")
- environment variable: `echo $PATH`; `vim ~/.zshrc`
  - `$PATH`可以作为输入
- Connecting Programs:
  - `< file` and `> file` can rewrite the input and output streams of a program to a file
  - `>>` 可append 
  - `|` lets you "chain" programs such that the output of one is the input of another.
- [Shell中不同类型quates的含义](https://www.gnu.org/software/bash/manual/html_node/Quoting.html) 