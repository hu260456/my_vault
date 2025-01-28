#### 编译选项

| 编译选项           | 说明                   |
| -------------- | -------------------- |
| E              | 生成预处理文件（i）           |
| S              | 编译生成汇编文件（s）          |
| c              | 汇编生成目标文件（o）          |
| I              | 指定头文件目录              |
| g              | 生成调试信息               |
| D(MACRO)       | 定义宏 MACRO            |
| D(MACRO=ABC)   | 定义宏 MACRO，宏值为 ABC    |
| w              | 不显示任何警告              |
| Wall           | 显示所有警告               |
| O(n)           | 默认 O0，最大 O3，O0 不进行优化 |
| std=<standard> | 指定高级语言的版本            |
| ansi           | 使用ANSI C标准           |

#### 静态库

- 制作
	- `gcc -c <库源文件> -o <库目标文件>`
	- `ar src <lib库名.a> <若干库目标文件>`
- 使用
	- `gcc -c <程序源文件> -o <程序目标文件>`
	- `gcc <程序目标文件> -L <库文件目录> -l <待链接库名> -o out`

#### 共享库

> **PIC: Position-Independent Code**
> 
> 动态库中的代码必须是 PIC，静态库的代码可以是 PIC，也可以不是 PIC

- 制作
	- `gcc -fPIC(生成与位置无关的代码) -c <库源文件> -o <库目标文件>`
	- `gcc -shared <库目标文件> -o <lib库名.so>`
- 使用
	- `gcc -c <程序源文件> -o <程序目标文件>`
	- `gcc <程序目标文件> -L <库文件目录> -l <动态库名> -o out`

**还需让动态链接器能够找到动态库（三种方式）：**

- 将库路径写在 `/etc/ld.so.conf` 中，执行 `ldconfig` 重新加载
- `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<lib_path>`
- 将库放在 `/lib` 或 `/usr/lib` 中

## gdb

| 命令                                 | 备注                          |
| ---------------------------------- | --------------------------- |
| quit/q                             | 退出调试                        |
| set args arg1 arg2 ...             | 设置命令行参数                     |
| show args                          | 查看命令行参数                     |
| set list <num>                     | 设置list命令一次显示num行代码          |
| show list                          | 查看每次显示行数                    |
| list/l 1                           | 查看源文件，从第 1 行开始显示            |
| list/l                             | 查看main函数对应文件                |
| <Enter>                            | 按回车执行上一条命令                  |
| list/l app.c:<num>                 | 查看app.c文件第num行              |
| list/l app.c:main                  | 查看app.c文件的main函数            |
| list/l <function>                  | 查看function函数                |
| break/b num                        | 在第num行设置断点                  |
| break/b function                   | 在函数的第一行设置断点                 |
| break/b xxx if i==trigger          | 当i等于trigger的时候设置断点          |
| delete/del/d num                   | 删除断点num，num可以有多个            |
| delete/del/d start-end             | 删除断点start至断点end             |
| disable/dis xxx                    | 设置断点无效，可以有多个，可以指定区间         |
| enable/ena xxx                     | 设置断点生效，可以有多个，可以指定区间         |
| info/i break/b                     | 查看设置的断点                     |
| info/i display                     | 查看自动跟踪的变量                   |
| start/st                           | 从程序第一句代码开始调试                |
| run/r                              | 执行到下一个断点                    |
| run/r arg1 arg2 ...                | 先设置命令行参数，再执行 run            |
| print/p/d[/h/o/d/u/f/c/t（二进制）] var | 以特定格式打印整型变量var              |
| ptype var                          | 打印变量var的类型                  |
| display /format v                  | 跟踪变量 v，采用格式 format 打印       |
| undisplay i                        | 取消自动跟踪变量，i 是变量编号，可以多个，允许范围  |
| delete display i                   | 效果同上                        |
| disable dispaly i                  | 暂停自动跟踪变量，xxx是变量编号，可以多个，允许范围 |
| enable display i                   | 启动自动跟踪变量，xxx是变量编号，可以多个，允许范围 |
| continue/c                         | 继续执行                        |
| step/s                             | 跳进                          |
| finish                             | 跳出函数，需要函数内没有有效断点            |
| until                              | 跳出循环，需要循环内没有有效断点            |
| next/n                             |                             |
| set var var=value                  | 设置变量var的值为value             |
| backtrace/bt                       | 查看栈帧                        |
| frame num                          | 切换到栈帧 num                   |

