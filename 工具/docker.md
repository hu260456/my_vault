##### 容器和镜像

> 使用 Docker 安装应用时，Docker 会自动搜索并下载应用镜像。镜像不仅包含应用本身，还包含应用运行所需要的依赖、配置、环境等。Docker 会在运行镜像时创建一个隔离环境，称为容器（container）

##### 配置镜像加速

```shell
# 阿里云中找容器镜像服务 ACR
# 选择镜像加速器，会有操作文档
# 大概长这样：
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF' { "registry-mirrors": ["https://7cj0tj90.mirror.aliyuncs.com"] } EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

##### 命令

```Shell
# 启动|停止|重启|设置自动启动 docker
systemctl start|stop|restart|enable|disable docker

# 查看 docker 版本
docker version

# 查看 docker 信息
docker info

##### 查看本地镜像
docker images
docker images -a # 显示所有镜像
docker images -q # 只显示镜像ID

##### 拉取镜像
docker pull name:version

##### 搜索镜像
docker search name
docker search name --filter=stars=3000 # 过滤stars小于3000的镜像

##### 删除镜像
docker image rm name:version
docker image rm name:id
docker image rm -f name:version|id # 强制删除
# 也可以用 rmi 命令
docker rmi name:version

##### 删除容器
docker rm name
docker rm id
docker rm -f id # 强制删除

##### 创建容器
docker create name
##### 创建并运行容器
docker run name:version  # 不声明 version 默认代表 latest
docker run -d \  # 让容器在后台运行
--name mysql \  # 给容器起的名字，唯一
-p 10086:3306 \  # 配置端口映射，宿主机上端口:容器内端口
-p xxx:xxx \  # 可以创建多个映射
-e TZ=Asia/Shanghai \  # 设置环境变量
-e MYSQL_R00T_PASSWORD=123 \  # 可以设置多个
mysql:5.7

##### 查看容器
docker ps  # 查看运行中的容器
docker ps -a  # 查看所有容器

##### 启动 | 重启 | 停止 | 杀死容器
# 以下 name 也可以用 id 替换
docker start name
docker restart name
docker stop name
docker kill name

##### 查看日志
docker logs id
docker logs name
docker logs -f name # 实时显示

##### 进入一个容器
docker exec -it name bash # 获得一个容器的 bash 终端
docker exec -it name mysql -uroot -p # 以终端方式直接进入 mysql
exit  # 退出容器

##### docker cp 本地和容器之间传输文件
docker cp <本地目录> <容器:容器内的目录>
docker cp <容器:容器内的目录> <本地目录>

##### 详细查看一个容器（包括数据卷信息等）
docker inspect nginx

##### 保存镜像、从本地读取镜像
docker save -o nginx.tar nginx:latest
docker load -i nginx.tar
```

##### 数据卷

```shell
# 数据卷（volume）是一个虚拟目录，是容器内目录与宿主机目录之间映射的桥梁
# 一般在创建容器时挂载数据卷
# 挂载方式：docker run -v 数据卷:容器内挂载点
# 容器内目录挂载到宿主机任意目录: docker run -v 本地目录:容器内目录
# 可以多次写
docker run -v ... -v ...
# 若数据卷不存在将自动创建

# 查看关于数据卷的帮助信息
docker volume --help

# 创建数据卷一个随机名称的数据卷
docker volume create
# 创建数据卷
docker volume create volume_name

# 查看所有数据卷
docker volume ls

# 删除指定数据卷
docker volume rm

# 查看某个数据卷的详情
docker volume inspect

# 清除匿名数据卷
docker volume prune
```

##### 自定义镜像

- docker 镜像结构，只要描述好镜像结构，docker 就可以自动构建自定义镜像
	- 入口：镜像运行入口，一般是程序启动的脚本和参数
	- 层：添加安装包、依赖、配置等，每次操作都形成新的一层
	- 基础镜像：应用依赖的系统函数库、环境、配置、文件等

- Dockerfile
	- 是一个文本文件，其中包含一个个指令（Instruction），用指令来说明要执行什么操作来构建镜像
	- 将来 Docker 可以根据 Dockerfile 帮我们构建镜像
	- 部分指令如下:

|     指令     |             说明             |                                示例                                |
| :--------: | :------------------------: | :--------------------------------------------------------------: |
|    FROM    |           指定基础镜像           |                          FROM centos:6                           |
|    ENV     |      设置环境变量，可在后面指令使用       |                          ENV key value                           |
|    COPY    |       拷贝本地文件到镜像的指定目录       |                     COPY ./jre11.tar.gz /tmp                     |
|    RUN     | 执行Linux的shell命令，一般是安装过程的命令 | RUN tar -zxvf /tmp/jre11.tar.gz && EXPORTS path=/tmp/jre11:$path |
|   EXPOSE   |   指定容器运行时监听的端口，是给镜像使用者看的   |                           EXP0SE 8080                            |
| ENTRYPOINT |   指定容器运行时监听的端口，是给镜像使用者看的   |                   ENTRYPOINT java -jar xx.jar                    |

```shell
# 构建 docker 镜像
# 不指定 tag 则默认为 latest
docker build -t myImg:tag <Dockerfile文件所在目录>
```

##### 自定义网络

默认情况下，所有容器都是以 bridge 方式连接到 Docker 的一个虚拟网桥上

![](attachments/Pasted%20image%2020240714013332.png)

加入自定义网络的容器才可以通过容器名互相访问（可以直接 ping 容器名）（ip 会变，容器名不会变，good），Docker 的网络操作命令如下：

| 命令                        | 说明           |
| ------------------------- | ------------ |
| docker network create     | 创建一个网络       |
| docker network ls         | 查看所有网络       |
| docker network rm         | 删除指定网络       |
| docker network prune      | 清除未使用的网络     |
| docker network connect    | 使指定容器连接加入某网络 |
| docker network disconnect | 使指定容器连接离开某网络 |
| docker network inspect    | 查看网络详细信息     |

```shell
# 创建容器的时候为容器指定一个 ip 地址
docker run --name nginx --ip=172.18.0.2 nginx
# 创建一个网络 mynet
docker network create mynet
# 让容器 nginx 加入 mynet
docker network connect mynet nginx
# 在创建容器的时候就加入 mynet（默认的网络就不加入了）
docker run --network mynet
```

##### docker compose

> DockerCompose 通过一个单独的 docker-compose.yml 模板文件（YAML格式）来定义一组相关联的应用容器，帮助我们实现多个相互关联的 Docker 容器的快速部署。常用作多容器的项目部署以及集群部署。

```yaml
# 项目版本
version: "3.8"

# 项目服务
services:
	mysql:
		iamge:mysql
		container_name: mysql
		ports:
			- "3306:3306"
		environment:
			TZ: Asia/Shanghai
			MYSQL_ROOT_PASSWORD: 123456
		volumes:
			- "./mysql/conf:/etc/mysql/conf.d"
			- "./mysql/data:/var/Lib/mysql"
			- "./mysql/init:/docker-entrypoint-initdb.d"
		networks:
			- my_net
	nginx:
		...

# 配置网络
networks:
	...
```

- docker compose 命令

`docker compose [options] [command]

| 类型       | 参数或指令   | 说明                |
| -------- | ------- | ----------------- |
| Options  | -f      | 指定compose文件的路径和名称 |
| Options  | -p      | 指定project名称       |
| Commands | up      | 创建并启动所有service容器  |
| Commands | down    | 停止并移除所有容器、网络      |
| Commands | ps      | 列出所有启动的容器         |
| Commands | logs    | 查看指定容器的日志         |
| Commands | stop    | 停止容器              |
| Commands | start   | 启动容器              |
| Commands | restart | 重启容器              |
| Commands | top     | 查看运行的进程           |
| Commands | exec    | 执行容器命令            |
