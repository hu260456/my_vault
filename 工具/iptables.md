#### 环境准备

> iptables 已内置到 linux 内核中，只需要安装 `iptables-services` 来管理服务

`apt install iptables-services  # 这个程序和 firewalld, ufw 互斥, 需要手动关闭它们`

#### 防火墙概述

- 防火墙功能
  - 封端口、封 ip
- 实现 NAT 功能
  - 共享上网
  - 端口映射

#### 防火墙的类型

- 硬件防火墙
  - 整个企业的入口
- 软件防火墙
  - iptables
  - firewalld
  - ufw
- 云防火墙
  - 阿里云安全组

#### 容器、表、链和规则

- 容器
- 表 table
	存放链的容器
	防火墙的最大概念
- 链 chain
	存放规则的容器
- 规则 policy

iptables 一般默认是==四表五链==
- 表: filter, nat, raw, mangle
- 链: input, output, forward, prerouting, postroutine

#### filter 表

- 是 iptables 的默认表
- 用来屏蔽/放行 端口/ip

- filter 表定义了 3 个链

|  INPUT  | 负责过滤所有目标地址是本机地址的数据包，通俗来说，就是过滤进入主机的数据包（能否让数据包进入服务器） |
| :-----: | :------------------------------------------------- |
| FORWARD | 负责转发流经主机的数据包，和 NAT 关系很大，net.ipv4.ip_forward=0      |
| OUTPUT  | 处理所有源地址是本机地址的数据包，通俗的讲，就是处理从主机发出去的数据包               |

#### nat 表

- 一般用途
	- 实现共享上网（内网服务器上外网）
	- 端口映射和 ip 映射

- nat 表定义了 3 个链

| OUTPUT      | 和主机放出去的数据包有关，改变主机发出数据包的目的地址 |
| ----------- | --------------------------- |
| PREROUTING  | 作用是改变数据包的目的地址、目的端口等         |
| POSTROUTING | 作用是改变数据包的源地址，源端口等           |

#### 规则的匹配

> 防火墙是层层过滤的，是按照配置规则的顺序从上到下，从前到后进行过滤的

![](attachments/Pasted%20image%2020240716162017.png)

#### 一个请求的历程

![](attachments/Pasted%20image%2020240716165857.png)


```shell
# 开放 80 端口
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```