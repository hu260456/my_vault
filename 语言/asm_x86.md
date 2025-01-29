---
date: 2025-01-29
---
# x86 汇编

#### DS

```Shell
##### 例1
mov bx, 1000H
mov ds, bx # 1000H不能直接送到ds中
mov al, [0] # 将10000H中的数据读到al中
mov al, ds:[0] # 引入段前缀，有些机器编译时可能会把[0]当作常数
##### 例2
mov bx, 1000H
mov ds, bx
mov [0], al # 将al中的数据写到10000H中
```

#### SI、DI

```Shell
# 变址寄存器
# SI: source index, 源变址寄存器
# DI: destination index, 目标变址寄存器
```

#### 栈

```Shell
##### 以字为单位操作
# 将ax中的数据送入栈中
# 执行后： sp = sp - 2
push ax
# 弹栈并送入ax
# 执行后： sp = sp + 2
pop ax
# ss：栈段寄存器 —— 存放栈顶的段地址
# sp: 栈顶指针寄存器 —— 存放栈顶的偏移地址
##### 溢出问题

```

#### 伪指令

```Shell
# segment: 段定义
# end: 汇编程序的结束标记
# assume: 关联段与段寄存器
```

#### 汇编 | 链接

```Shell
# 汇编
masm
masm x(.asm);
# 链接
link x(.obj);
# 执行可执行程序
<可执行文件名>
```

#### 指令

