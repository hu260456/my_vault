#### config

```Shell
git config --global user.email "<你的邮箱>" // 配置global邮箱
git config --global user.name "<你的名字>" // 配置global姓名
git config --global --unset http.proxy // 重置代理
```

#### init

```shell
# 初始化本地仓库
cd <项目目录>
git init
```

#### add

```Shell
git add <file> # 添加到暂存区
git add <dir> # 添加目录到暂存区，含子目录
git add . # 添加当前目录所有文件/目录到暂存区
```

#### status

```Shell
git status # 查看自上次提交后的改动
git status -s # 简要版
```

#### commit

```Shell
git commit -m "comment" # 提交暂存区所有文件到仓库区
git commit <file> -m "comment" # 提交暂存区指定文件到仓库区
```

#### branch

```Shell
git branch    # 查看所有local分支
git branch -r # 查看所有remote分支
git branch -a # 查看所有local和remote分支
git branch -f <新分支> # 新建分支但不切换
git branch -d <local分支> # 删除本地分支
git branch -d -r <remote分支> # 删除remote分支
git branch -D <local分支> # 强制删除local分支
git branch -m <old> <new> # 重命名local分支old为new
```

#### checkout

```Shell
git checkout -b <新分支的名称> # 创建并切换到分支
```

#### push

```Shell
git push <remote主机名> <local分支名>:<remote分支名> # 推送到远程仓库
git push <remote主机名> <local分支名> # 如果local分支与remote分支名相同
git push --force <remote主机名> <local分支> # 强制push
```

