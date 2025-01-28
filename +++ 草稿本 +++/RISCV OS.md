#### RISC-V ISA 命名格式

> `RV[###][abc...xyz]`

- RV -> RISC-V
- \#### -> =32|64|128, 用于标识处理器的字宽，也就是寄存器的宽度
- 例子
	- RV32IMA：支持 I、M、A 模块的 32 位 RISC-V ISA

#### 设计原则：增量式和模块化

- 增量 ISA
	- 计算机体系结构的传统方法
	- 同一个体系架构下的新一代处理器不仅实现了新的ISA扩展，还必须实现过去的所有扩展，目的是为了保持向后的二进制兼容性
	- 典型的，以80x86为代表
- 模块化 ISA
	- 由 1 个强制要求实现的基本指令集 + 多个可选的扩展指令集组成
	- 基础指令集是固定的，不会改变
	- 如 RISC-V

#### RV 的基本指令集

- RV32I: 32 位整数指令集
- RV32E: RV32I 的子集，==常用于小型嵌入式==
- RV64I: 64 位整数指令集，==兼容 RV32I==
- RV128I: 128 位整数指令集，==兼容 RV32I 和 RV64I==

#### RV32I 指令编码格式

![](attachments/Pasted%20image%2020240714171608.png)

- 指令长度：ILEN1 = 32 bits (RV32I)
- 指令对齐：IALIGN = 32 bits (RV32I)
- 32 个 bit 划分成不同的 "域（field)"
- funct3, funct7 和 opcode 一起决定最终的指令类型
- 指令在内存中按照==小端序==排列

#### RV ISA 的可选模块举例

> 特定组合“IMAFD”被称为“通用”组合，用“G”表示

![](attachments/Pasted%20image%2020240712152806.png)

#### 通用寄存器

- US(Unprivileged Specification) 定义了32个通用寄存器（x0~x31）以及一个 PC
- RV 中 PC 对程序员透明
- 如果实现支持 F/D 扩展则需要额外支持 32 个浮点(Float Point)寄存器
- RV32E 将 32 个通用寄存器缩减 16 个
- RVn 的寄存器宽度为 n 位
- 寄存器 x0 不可以写，读值永远为 0

#### HART(HARdware Thread)

- 硬件线程
- 是 RV 中的概念
- ==在执行算术逻辑运算时所操作的数据必须直接来自寄存器==

#### 特权级别

- M 级别是最高的级别，所有的实现都需要支持
- M 模式下访问的是物理地址，相当于 x86 中的实模式
- 不同的特权级别下时分别对应各自的一套 Control and Status Register(CSR)，用于控制和获取相应 Level 下的处理器工作状态
- 高级别的特权级别下可以访问低级别的 CSR
- RISC-V 定义了专门用于操作 CSR 的指令
- RISC-V 定义了特定的指令用于在不同特权级别之间进行切换

| Level | Encoding |       Name       | Abbreviation |
| :---: | :------: | :--------------: | :----------: |
|   0   |    00    | User/Application |      U       |
|   1   |    01    |    Supervisor    |      S       |
|   2   |    10    |     Reserved     |              |
|   3   |    11    |     Machine      |      M       |

#### 内存管理和保护

##### 物理内存



##### 虚拟内存

- 需要支持 ==`S`== 特权级别


#### ELF(Executable Linkable Format)

> ELF 是一种 Unix-like 系统上的二进制文件格式标准。

ELF 标准中定义的采用ELF格式的文件分为4类：

| ELF 文件类型 |                                                                     说明                                                                     |        实例        |
| :------: | :----------------------------------------------------------------------------------------------------------------------------------------: | :--------------: |
|  可重定位文件  |                                                       内容包含了代码和数据，可以被链接成可执行文件或共享目标文件。                                                       |  Linux 上的 .o 文件  |
|  可执行文件   |                                                                 可以直接执行的程序                                                                  | Linux 上的 .out 文件 |
|  共享目标文件  | 内容包含了代码和数据，可以作为链接器的输入，在链接阶段和其他的 Relocatable File或者 Shared Object File一起链接成新的ObjectFile；或者在运行阶段，作为动态链接器的输入，和 Executable File 结合，作为进程的一部分来运行 | Linux 上的 .so 文件  |
|  核心转储文件  |                                               进程意外终止时，系统可以将该进程的部分内容和终止时的其他状态信息保存到该文件中以供调试分析。                                               | Linux 上的 core 文件 |

ELF 文件格式：

![](attachments/Pasted%20image%2020240713145650.png)

- ELF Header
	- 包含 ELF 文件的基本信息，程序所运行的体系架构等
- Program Header Table
	- 描述了运行视图
- 白色的部分被称为一个个 Section（节）
- Section Header Table
	- 描述了链接视图

处理 ELF 文件的工具 Binutils：

- ar
- as
- ld
- objcopy
- objdump
- readelf
	- readelf -h hello.o
- ...

```shell
# 读取 hello.o 中的 header 信息
readelf -h hello.o

# 查看 hello.o 中的 section 信息

readelf -S hello.o
readelf -SW hello.o  # 更便于观看(Wide)

# 反汇编（需要携带调试信息）
objdump -S hello.o
```

#### 交叉编译

参与编译和运行的机器根据其角色可以分成以下三类:

- 构建(build)系统：生成编译器可执行程序的计算机
- 主机(host)系统：运行编译器可执行程序，编译链接应用程序的计算机系统
- 目标(target)系统：运行应用程序的计算机系统

根据 build/host/target 的不同组合我们可以得到如下的编译方式分类:

- 本地(native)编译：build == host == target
- 交叉(cross)编译：build == host != target

##### 交叉编译例子

```shell
riscv64-unknown-elf-gcc \
-march=rv32ima \  # 指定目标指令集架构
-mabi=ilp32 \
hello.c
```

#### 模拟器 QEMU

QEMU 有两种主要运作模式

- usermode：直接运行应用程序
- systemmode：模拟整个计算机系统，包括中央处理器及其他周边设备

##### usermode

```shell
# 直接运行交叉编译生成的 a.out
qemu-riscv32 ./a.out
```

#### RISC-V 汇编

==以下介绍均采用 GNU 汇编器==

##### 语句格式

`[label:] [operation] [comment]`

- label: 标签
- operation
	- 指令
		- 对应二进制机器指令的字符串
	- 伪指令
		- 为了提高编程效率，可以用一条伪指令指示汇编器产生多条实际的指
	- 指示
		- 通过类似指令的形式(以"."开头)，通知汇编器如何控制代码的产生等
		- 不对应具体的指令
		- 属于汇编器定义的语法
	- macro（宏）
- comment: 注释

##### ADD

- add 指令是 RType 指令
- `ADD RD, RS1, RS2`