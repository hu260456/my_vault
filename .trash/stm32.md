---
mindmap-plugin: basic
---

# stm32

## 启动
- Sub title
	- 配置 boot 引脚选择启动方式
- Sub title
	- 在 STM32F10x 里，可以通过 BOOT1:0] 引脚选择三种不同启动模式

## 系统架构
- 内核 DCode 总线
	- 通过外部 DCode 总线连接到总线矩阵然后与 Flash 接口连接，实现从 Flash 加载常量和调试访问
- 内核 ICode 总线
	- 通过外部 ICode 总线连接 Flash，实现指令的读取
- 内核系统总线 Sbus
	- 通过外部的 System 总线连接到总线矩阵
- 通用 DMA1、通用 DMA2
	- 通过 DMA 总线，连接到总线矩阵
	- 作用是降低 CPU 负担
- 内部 SRAM
	- 存储程序执行时用到的变量
- FSMC(Flexible Static Memory Controller)
	- 用来扩展外部 SRAM，Flash，连接 LCD 屏幕等
- 内部 Flash
	- 存储下载的程序
	- 存储程序的全局常量
- AHB 到 APB 的桥
	- 桥 1
		- 通过 APB2 总线连接到 APB2 上的外设，最高 72MHz
	- 桥 2
		- 通过 APB1 总线连接到 APB1 上的外设，最高 36MHz

## GPIO 特点
- 不同型号，IO 口的数量可能不一样。
- 快速翻转，最快可以达到每 2 个时钟周期翻转一次。（STM32F1xx 系列最快可以达到 50MHz 的翻转速度）
- 每个 IO 都可以作为外部中断
- 支持 8 种工作模式。
	- GPIO 端口的每个引脚可以由软件分别配置成8种模式
		- 输入模式
			- 输入浮空（Input floating）
			- 输入上拉（Input pull-up)
			- 输入下拉（Input pull-down）
			- 模拟输入（Analog）
		- 输出模式
			- 通用开漏输出（Output open-drain）
			- 通用推挽式输出（Output push-pull）
			- 推挽式复用功能（Alternate function push-pull）
			- 开漏复用功能（Alternate function open-drain）
- 每个 I/O 端口位可以自由编程，I/O 端寄存器必须按 32 位字被访问。

## GPIO 模式
- Sub title
	- 施密特触发器（肖特基触发器）
- 输出模式
	- Sub title
		- 输出驱动器被激活
		- 施密特触发器输入被激活
		- 引脚上的数据在每个 APB2 时钟被==采样==到 IDR
	- 推挽输出
		- 工作时两个 MOS 管交替工作来驱动负载
		- 输出 1 时，上部晶体管导通将电流推向负载
		- 输出 0 时，下部晶体管导通将电流拉回地线
		- ==读 IDR 可得到最后一次输出的值==
		- 适用于驱动能力需求较高、高速信号传输、无需公用信号线的场合
	- 开漏输出
		- **PMOS 永远关闭**
		- 读 IDR 可得到 IO 状态
		- 输出 1 时须接外部上拉电阻
		- 不接上拉电阻则拉低为 0
		- 适用于多个设备共用信号线、不同电压系统、需要外部上拉电阻的场合
- 输入模式
	- **输出驱动器关闭**

## GPIO 寄存器
- 32 位配置寄存器 GPIOx_CRL、GPIOx_CRH
- 32 位数据寄存器 GPIOx_IDR、GPIOx_ODR
- 32 位置位/复位寄存器
- 16 位复位寄存器
- 32 位端口配置锁定寄存器

## 中断系统
- NVIC 嵌套向量中断控制器
	- 用 4 个位来控制优先级
	- 把优先级分为抢占优先级和响应优先级
	- 优先级值越小，优先级越高
	- 如果不设置优先级，则默认优先级为 0
- EXTI 外部中断控制器

## $I^2C$ 协议
- Sub title
	- $I^2C$: Inter-Integrated Circuit，内部集成电路
- Sub title
	- 特点：引脚少，硬件实现简单，可扩展性强
	- 速率
		- standard: 100 Kbps
		- fast: 400 Kbps
		- high speed: 3.4 Mbps，大多数 I2C 设备不支持此速率
	- 物理层
- Sub title
	- 协议层

## 定时器
- Sub title
	- 分类
		- 16位通用定时器
		- 24位看门狗定时器（==递减型==计数器）
		- PWM 控制定时器
		- 16位基本定时器用于驱动 DAC
- Systick
	- 系统定时器属于 CM3 内核，内嵌在 NVIC 中，是一个 24bit ==递减型==计数器，计数频率等于系统时钟
- 基本定时器
	- 是==递增型==计数器
	- 没有外部 IO，只能计数，不能对外部脉冲计数
- 通用定时器
	- 覆盖基本定时器的所有功能
	- 支持向上计数、向下计数、中央对齐模式（向上和向下计数）
	- 可以对外部脉冲计数
	- 可以输出比较
	- 可以生成 PWM