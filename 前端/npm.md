#### npm

```shell
# 查看包安装目录
npm root
# 查看全局包安装目录
npm root -g

# 安装所有依赖
npm install
# 安装包
npm install <package_name>
# 安装包，仅开发
npm install -D <package_name>

# 卸载包
npm uninstall <package_name>

# 更新项目依赖
npm update <package_name>
# 更新全局包
npm update -g <package_name>

# 运行脚本
npm run dev

# 设置镜像，以淘宝镜像为例
npm config set registry https://registry.npm.taobao.org
# 使用镜像
npm install <package_name> --registry=https://registry.npm.taobao.org
# 打开 npm 配置文件
npm config edit

# 创建一个新项目
npm init
npm create

# 创建一个某项目，init 是 create 的别名
npm init vue
npm create vue

# 登录 npm
npm login
```

#### cnpm

```shell
# cnpm's installation
npm i -g cnpm
```

#### yarn

```shell
# yarn's installation
npm i -g yarn

# 安装所有依赖
yarn
# 安装包
yarn add <package_name>
# 安装包，仅开发
yarn add -D <package_name>

# 卸载包
yarn remove <package_name>

# 运行脚本
yarn dev

# 获取二进制文件地址以添加环境变量
yarn global bin
```

#### pnpm


```shell
# pnpm's installation
npm install -g pnpm

# 创建项目
pnpm create vue

# 安装所有依赖
pnpm install
# 安装包
pnpm add <package_name>
# 安装包，仅开发
pnpm add -D <package_name>

# 卸载包
pnpm remove <package_name>

# 运行脚本
pnpm dev
```