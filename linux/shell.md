#### 命令解释器概述

- `bash` 是 `shell` 的其中一个实现
- `shell` 是 系统的`命令解释器`
- 查看系统支持的 shell
	- `cat /etc/shells`
- 在 shell 脚本的开头一般会写上一个`shebang(#!)`，后面跟上某个解释器的路径
	- 举例：`#! /usr/bin/bash`
	- 用来指示用特定的解释器解释此脚本
	- 如果没有指定解释器，默认使用 `$SHELL`

#### source 和 bash(sh) 的区别

- source 和 “./script.sh” 会直接在当前 shell 上下文运行脚本
- bash(sh) 会在一个新的 shell 上下文中运行脚本，产生的变量不保存

#### 内置命令和外置命令

> 使用 `type <命令>` 查看其类型，内置命令显示有 builtin 字样，使用 `compgen -b` 查看所有 builtin 命令

- 内置命令
	- 在系统启动时就加载入内存，常驻内存，执行效率更高，但是占用资源
	- 是 shell 的一部分
	- 不会开启子进程执行
- 外部命令
	- 需要从硬盘中读取程序
	- `/bin /usr/bin /usr/sbin /sbin` 这些目录通常存放外置命令
	- 会开启子进程执行

#### 变量

```Bash
# 变量默认是字符串
# 以下三种定义方式等价
name="张三"
name='张三'
name=张三

# 取消定义变量
unset name

# 设置只读变量
readonly age=20

# 引用变量的两种方法
echo $name
echo ${name}

# 单引号是纯字符串
name2='$name'
# 双引号识别变量，可以使用 '\' 转义符
name3="$name"
# 反引号可以引用一个命令的执行结果
result=`ls`
# $() 相当于 ``
result=$(ls)

# 返回变量的值
${var}
# 返回变量长度
${#var}
# 返回变量从第 start 个字符开始的字符
${var:start}
# 提取变量从第 start 个字符开始的 length 个字符
${var:start:length}
# 从变量开头，删除最短匹配的 word 子串
${var#word}
# 从变量开头，删除最短的满足 a 开头 b 结尾的子串
${var#a*b}
# 从变量开头，删除最长匹配的 word 子串
${var##word}
# 从变量结尾，删除最短匹配的 word 子串
${var%word}
# 从变量结尾，删除最长匹配的 word 子串
${var%%word}
# 用 string 代替第一个匹配的 pattern
${var/pattern/string}
# 用 string 代替所有的 pattern
${var//pattern/string}
# 如果 var 空，name=word
name=${var:-word}
# 如果 var 空，name=word，var=word
name=${var:=word}
# 如果 var 空，name 未能设置，word >> stderr
name=${var:?word}
# 如果 var 空，无操作，否则 name=word
name=${var:+word}
```

#### 环境变量

```Shell
# 临时添加环境变量
export age=20

# 永久添加环境变量
# 1. 修改用户个人配置文件
vim ~/.bash_profile
vim ~/.bashrc

# 2. 修改全局配置文件
vim /etc/profile
vim /etc/bashrc
```

#### 环境变量加载顺序

1. 通过 ssh 登录 Linux 后，系统会启动一个 bash shell，bash 读取若干个系统环境文件
2. 运行 /etc/profile
3. 运行 ~/.bash_profile
4. 运行 ~/.bashrc
5. 运行 /etc/bashrc

#### 检查环境变量相关命令

```Shell
set # 输出所有变量，包括局部变量、全局变量
env # 只显示全局变量
declare # 如同 set
export # 显示和设置环境变量

unset <变量名> # 取消设置环境变量

readonly # 显示或设置只读变量，随着 shell 的关闭而销毁
```

#### 一些环境变量

| 变量            | 备注                  |
| ------------- | ------------------- |
| SHELL         | 当前解释器               |
| HISTSIZE      | 用户历史命令记录大小          |
| HISTFILE      | 存放用户执行历史命令的文件       |
| BASH_SUBSHELL | 记录当前是否是 subshell 环境 |
| USER          | 当前用户名               |
| UID           | 当前 UID              |
| HOME          | 当前用户 HOME           |
| RANDOM        | 一个随机数               |

#### 一些特殊变量

```Shell
!$
# 把上一条命令的参数作为标准输入
!!
# 运行最近一次执行的命令
$0
# 这个程式或脚本的名字
$n
# 这个程式的第n个参数值，n=1…9，超过10个加一对花括号把参数引起来 eg. ${11}。
$*
# 这个程式的所有参数，得到一个整体
$@
# 这个程式的所有参数，得到每一个独立参数
$#
# 这个程式的参数个数
$$
# 当前 shell 进程 PID
$!
# 获取上一次后台运行的进程的 PID
$?
# 上一个命令的返回状态，0 表示正常退出
$-
# 显示shell使用的当前选项，与set命令功能相同
#
#@ 跟 @ 跟 @跟*类似，但是可以当作数组用
$_
# 代表上一次执行的命令的最后一个参数
```

#### 数值运算

```shell
# 支持 + - * / % ** 运算
# 支持 ++ -- 操作
# 支持 && || ! 逻辑运算
# 支持 < <= > >= ==(=) != 比较
# 支持 << >> ~ | & ^ 位操作
# 支持 += -= *= /= %= 等

# 在 (()) 内进行数值运算，使用 $ 符号提取结果
num=1
((num++))
echo $((num>1||num%5))

# let 的作用类似 (())
let num=1
let num++
echo $num
```

#### 数组

**只支持一维数组**

```shell
# 方式 1
declare -a array
array[n]=value

# 方式 2
array=(item1 item2)

# 对数组的引用
${array[n]}

# 获取数组的所有元素
${array[@]}
${array[*]}

# 获取数组长度
${#array[@]}
${#array[*]}
```

#### if

```shell
# [ expression ] 条件测试相当于 test expression 条件测试
[ -e $filename ] && echo '文件存在'
# 使用 [] 条件测试需要转义特殊符号
[ 1 \< 2 ] && echo 'test'

# 使用 [[]] 条件测试不需要转义特殊符号
[[ -e $filename ]] && echo '文件存在'
# 支持正则，num 是 1|2|3
[[ ! $num =~ [1-3] ]] || echo '非法范围'

# if num 为空
if [ -n num ]; then
	...
else
	...
fi

# if num != 0
if [ num -nq 0 ]; then
	...
else
	...
fi

# and gate condition
if [ condition1 ] && [ condition2 ]; then
	...
elif [ condition3 ]; then
	...
else
	...
fi
```

#### for

```Shell
# 写在一行
for var in {1..100}; do ...; done

# 脚本
for var in {1..100}
	do
		...
done

# 没有 in ... 时，相当于 in $@
for var
	do
		...
done

# 类似于 C 的 for
for ((expr1; expr2; expr3))
	do
		...
done

# 循环控制
for var in {1..100}
	do
		...
		# 退出当前循环
		break
		# 如果是嵌套循环，break 后面可以跟一数字 n，表示退出第 n 重循环，最里面的为第一重循环
		break n
		# 直接进入下一个循环
		continue
		# 如果是嵌套循环，continue 后面也可跟一数字 n，表示回到下一个第 n 重循环的顶部
		continue n
done
```

#### while

```shell
# 写在一行
while cond; do ...; done

# 脚本
while cond
	do
		...
done

# 永真循环
while:
	...
done
```

#### case

```shell
case expr in
	pattern1)
		...
	;;
	pattern2)
		...
	;;
	*)
		...
	;;
esac
```

#### until

```shell
until expr
	do
		...
done
```

#### select

```shell
select var in list
	do
		...
done
```

#### 函数

```shell
# 定义函数
function fn() {
	# 定义局部变量
	local var='test'
	# 获取参数
	echo $1 $2 $3
	# 退出 shell，返回状态码
	exit 1
	# 退出函数，返回返回值
	return 1
}

# 也可以
function fn{
	...
}

# 也可以
fn() {
	...
}

# 执行函数
fn
fn arg1 arg2 ...
```