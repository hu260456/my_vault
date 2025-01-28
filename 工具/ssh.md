#### 命令

```shell
# 查看 ssh 版本
ssh -V
# 确认 sshd 状态
systemctl status sshd
# 测试连接
ssh -i <私钥路径> -T user@ip
```

#### ssh 配置

> 在 ~ 下存在 .ssh 目录
> 在 /etc 下存在 ssh 目录

**ssh 配置目录**

- config
- known_hosts
	- 存放着已知的主机
- known_hosts.old
- atherized_keys
	- 存放公钥的文本

#### 密钥对

生成密钥对:
`cd ~/.ssh`
`ssh-keygen`

建议指定使用安全的 ed25519 算法
`ssh-keygen -t ed25519`

为密钥对添加注释
`ssh-keygen -C "这是... "`

会提问文件的保存路径，完全一样将覆盖先前的密钥对
passphrase 相当于二次密码校验，置空即可

完成后将生成两个文件，pub 文件是公钥，另一个是私钥

#### 将公钥发送到服务端

`ssh-copy-id -i <本地公钥路径> user@server_ip`

#### 使用私钥登录

`ssh -i <私钥路径> user@server_ip`

#### 禁用密码登录

找到 ==`sshd_config`== 文件，可能在 /etc/ssh 内

找到 PasswordAuthentication 并设置为 no，若保存了这里千万不能断开连接，因为密码已经不能登陆了，但是密钥对也无法登录

找到 PubkeyAuthentication 并设置为 yes

进入 sshd_connfig.d，查看里面的配置文件是否与上面设置的一致

`service ssh restart`

#### config 文件的配置

```plaintext
Host 名字
  HostName 地址
  User 用户
```

可以直接使用 `ssh 名字` 登录远端

#### 监听 22 端口

```shell
vim /etc/ssh/sshd_config
# 将 Port 22 解除注释，重启服务
# 根据需要选择登陆方式
# 可能需要安装 openssh-server
```

#### 清除已知主机的密钥

`ssh-keygen -R 192.168.161.132`