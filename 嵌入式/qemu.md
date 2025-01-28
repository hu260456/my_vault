#### 安装 qemu

```shell
# 适用于 Debian-based 发行版
sudo apt install qemu-system
```

#### 创建虚拟磁盘镜像

```shell
qemu-img create -f qcow2 disk.img 10G
# -f qcow2: 使用动态分配空间的镜像格式（推荐）
# disk.img: 文件名
# 10G: 镜像最大容量
```

#### 启动虚拟机

```shell
qemu-system-x86_64 -m 2G -hda disk.img -cdrom debian.iso
# -m 2G: 分配 2GB 内存
# -hda disk.img: 使用 disk.img 作为主硬盘
# -cdrom debian.iso: 挂载 ISO 镜像作为光驱
# -smp <CPU core number>
# -boot <d/c>
# -vga std: 启用标准图形输出
# -net nic -net user: 启用网络（NAT 模式）
# -enable-kvm: 启用 KVM 加速（需硬件支持）
# -net user,hostfwd=tcp::2222-:22 -net nic  ：端口转发（将主机端口映射到虚拟机）
```

#### 快照管理

```shell
qemu-img snapshot -c snapshot1 disk.img  # 创建快照
qemu-img snapshot -l disk.img            # 列出快照
qemu-img snapshot -a snapshot1 disk.img  # 恢复快照
```