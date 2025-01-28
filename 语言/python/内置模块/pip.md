#### 基本命令

```shell
# 安装特定版本的 X
pip install X==6.1.0

# 查看已安装的包，包括基本包
pip list
# 查看已安装的包，不包括基本包
pip freeze
```

#### virtualenv

```shell
# 安装基本包
# windows 系统需要额外安装 virtualenvwrapper-win
pip install virtualenv

# 查看已有虚拟环境
workon

# 进入虚拟环境
workon <虚拟环境>

# 创建新的虚拟环境
mkvirtualenv <虚拟环境名>

# 删除虚拟环境
rmvirtualenv <虚拟环境名>
```

#### venv

- 也可以用于创建和管理虚拟环境

