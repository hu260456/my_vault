#### 创建 workspace

```Shell
mkdir -p ws_name/src
cd ws_name
catkin_make
```

- ws 的结构

    - `build`: 构建空间，存放 CMake 和 catkin 的缓存、配置信息和其他中间文件
    - `devel`: 开发空间，存放编译后生成的目标文件，包括头文件、库文件、可执行文件等
        - setup.bash
    - src
        - pkg0
            - `include`: 存放 C++ 头文件
            - `config`: 存放 YAML 配置文件
            - `launch`: 存放 .launch 文件
            - `src`: 存放 C++ node
            - `scripts`: 存放 Python node | shell 脚本
            - `msg`: 存放自定义消息协议
            - `srv`: 存放自定义 service
            - `acton`: 存放自定义 action
            - `package.xml`: 记录 pkg 相关信息，通常添加 ros 依赖
        - pkg1, 2, ...

#### 创建 package

```Shell
# <ws_name>/src
catkin_create_pkg <pkg_name> rospy roscpp rosmsg
# 依赖 std_msgs 是标准依赖库
```

#### 编译项目

```Shell
# ws_name
catkin_make
```

#### 创建 Python 节点

```Python
# 一些 ros 版本可能需要在 package 的目录下的 CMakeLists 放开如下注释
# 修改，并在 ws 重新编译
# catkin_install_python(PROGRAMS
#   scripts/<node_name>.py
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

# <pkg_name>/scripts/<node_name>.py
#! /usr/bin/env python3
#coding: utf-8  # python2默认编码为ASCII
import rospy
if __name__ == '__main__':
    # 创建并初始化节点
    rospy.init_node('node_name')
    # 控制台输出消息
    rospy.loginfo('节点创建成功')
```

#### 创建 CPP 节点

```C++
// 可能需要在 package 的目录下的 CMakeLists 放开如下注释，修改，并在 ws 重新编译
// 1.
add_executable(<节点名> <节点cpp文件>)
// 2.
target_link_libraries(<节点名>
  ${catkin_LIBRARIES}
)
// 处于未知原因，如果 vscode 没有对 ros 库的代码补全
// 可能需要将 .vscode/c_cpp_properties.json 中的 cppStandard 改为 c++17

// <pkg_name>/src/<node_name>.cpp
#include "ros/ros.h"
int main(int argc,  char **argv) {
    // 解决中文乱码
    setlocale(LC_ALL, "");
    // 初始化ros节点
    ros::init(argc, argv, "node_name");
    // 控制台输出消息
    ROS_INFO("节点创建成功");
}
```

#### 运行节点

```Shell
# 以 Python 为例
# 为 scripts 中的源码追加 x 权限
rosrun pkg_name node_name
```

#### 命令行工具

```Shell
################ rosnode ################
# 打印当前活动节点
rosnode list
# 显示节点信息
rosnode info /node_name
# 检查节点网络连通性
rosnode ping /node_name
# 杀死节点
rosnode kill /node_name
# 将无法访问的节点（僵尸节点）的信息清除
rosnode cleanup
# 查询某台主机上相关的节点信息
rosnode machine <主机名>
########## rostopic ##########
# 打印当前活动广播
rostopic list
# 打印某个 topic 的消息
rostopic echo /<topic_name>
# 打印某个 topic 的数据类型
rostopic type /<topic_name>
# 查看某个 topic 的详情
rostopic info /<topic_name>
# 统计某个 topic 的平均频率
rostopic hz /<topic_name>
# 显示某个 topic 的 bandwidth（带宽）
rospotic bw /<topic_name>
# 给某个 topic 发送一次消息
rostopic pub /<topic_name> <data_type> <data>
# 以频率 rate 给某个 topic 发送消息
rostopic pub -r <rate> /<topic_name> <data_type> <data>
################ rosservice ################
# 列出所有服务
rosservice list
# 发起一个请求
rosservice call <srv_name> <request>
# 列出服务的相关信息
rosservice info <srv_name>
# 打印服务使用的数据类型
rosservice type <srv_name>
# 打印服务的 ROSRPC uri 资源路径
rosservice uri <srv_name>
################ rosmsg  ################
# 查看所有消息类型
rosmsg list
# 查看某个消息类型，两种方法一样：
rosmsg show <type>
rosmsg info <type>
################ rossrv ################
# 不要与 rosservice 记混，与 rosmsg 类似，它于消息类型相关
# srv 消息是 ROS service 通讯节点间传递的内容
# 显示所有 srv 消息
rossrv list
# 显示某个服务的 srv 消息，两种方式一样
rossrv info <node_name>/<srv_name>
rossrv show <node_name>/<srv_name>
################ rosparam ################
# 打印所有 key
rosparam list
# 设置参数
rosparam set <key> <value>
# 获取某个键的值
rosparam get <key>
# 删除某个参数
rosparam delete <key>
# 将所有参数序列化为字节存储到一个文件中
rosparam dump <文件名>.yaml
# 将一个文件反序列化，读取并设置所有参数
rosparam load <文件名>.yaml
```

#### launch

在 pkg 创建 launch/<launch_name>.launch，编写规则如下

```XML
<launch>
    <!-- 如果需要节点输出信息到屏幕上，设置 output 属性 -->
    <node pkg="包名" type="节点名" name="名称" output="screen"></node>
</launch>
```

运行 launch: `roslaunch <pkg_name> <launch_name>.launch`

#### ROS 文件系统命令

```Shell
########## roscore ##########
# 必须运行 roscore 才能使 ROS 节点间可通信
# roscore 将启动:
# - rosmaster
# - ros 参数服务器
# - rosout 日志节点
roscore
# 指定端口号
roscore -p X
########## rosrun ##########
# 运行指定 ROS 节点
rosrun <pkg> <node>
########## roslaunch ##########
# 执行某个包下的 launch 文件
roslaunch <pkg> <X.launch>
########## 增包 ##########
# 自定义包
catkin_create_pkg <pkg> <dep> ...
# 第三方包
apt install X
########## 删包 ##########
apt purge X
########## 查包 ##########
# 列举所有包
rospack list
# 查询一个包，如果存在则返回安装路径
rospack find <pkg>
# 进入某个包
roscd <pkg>
# 列出某个包下的文件
rosls <pkg>
# 网络搜索某个第三方包
apt search <X>
apt search ros-noetic-*  # 列举
```

#### Shell API

```Shell
# 进入 turtlesim 的目录
roscd turtlesim
```

#### Python API

```Python
# 阻塞当前线程
rospy.spin()
# 频率（Hz）
rate = rospy.Rate(1)
rate.sleep()
# rospy.is_shutdown()
# 如果使用 while True，则 Ctrl+C 不能终止循环，此 API 则可以
```

#### Topic 通信

- 节点与 master 之间使用 `RPC通信`

- 节点与节点之间使用 `TCP通信`

- 节点与节点建立联系后，无需再与 master 建立联系

```Shell
##### ros 提供了大量名为 rqt_x 的图像化工具 #####
# 使用 rqt_publisher 发布调试消息
rosrun rqt_publisher rqt_publisher
# 使用 rqt_topic 调试 publisher 发布的消息
rosrun rqt_topic rqt_topic
# 查看节点间的关系图
# 如果安装 ROS 的时候安装的不是完整版，需要自行安装
apt install ros-<distro>-rqt
apt install ros-<distro>-rqt-common-plugins
rosrun rqt_graph rqt_graph
```

- 发布

```Python
######### publisher_node.py
from std_msgs.msg import String
# topic_name 是唯一的
topic_name = 'hello'
queue_size = 100
publisher = rospy.Publisher(topic_name, String, queue_size=queue_size)

count = 0
rate = rospy.Rate(2)
# 广播数据
while not rospy.is_shutdown():
    msg = String()
    msg.data = 'data: {}'.format(count)
    count += 1
    publisher.publish(msg)
    rate.sleep()
```

- 订阅

```Python
######### subscriber_node.py
from std_msgs.msg import String
if __name__ == '__main__':
    rospy.init_node('subscriber_node')
    topic_name = 'hello'
    subscriber = rospy.Subscriber(topic_name, String, lambda msg: print(f'{msg}'))
    rospy.spin()
```

#### 自定义 msg

```Shell
##### 创建一个 pkg
catkin_create_pkg name_msgs rospy roscpp rosmsg
##### 初始化 pkg
cd <workspace>
catkin_make
##### 在 name_msgs 中创建文件夹名为 msg
##### 在 msg 中新建文件 <name>.msg
# msg 文件编写示例
string name
int32 age
Student leader  # 如果已经写了一个 Student.msg，可以使用自定义的 Student 类
Student[] members  # 如果需要使用数组，注意：ros 不支持二维数组
std_msgs/String intro  # 使用 std_msgs 中的 String
##### 编写 name_msgs 中的 package.xml
# 插入节点: 
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
##### 打开同级的 CMakeLists.txt
# 在 find_message 函数中加入 message_generation
# 找到 add_message_files 函数并放开注释
# 将 Message1.msg, Message2.msg 删除，追加 name.msg(msg文件夹下的文件的名字)
# 找到 generate_messages 函数并放开注释
# 找到 catkin_package，放开 CATKIN_DEPENDS roscpp rosmsg rospy 并追加 message_runtime
```

#### 服务通信

- 服务端启动后客户端才能发起请求
- 同 `Topic通信` 一样，发起端和接收端都需要向 master 注册信息

#### 自定义 srv

```Shell
##### 创建一个 pkg
catkin_create_pkg name_srvs roscpp rospy rosmsg
##### 在 name_srvs 中创建文件夹名为 srv
##### 在 srv 中新建文件 <业务名>.srv
# srv 文件编写示例
hello_msgs/Student stu
---  # --- 以前代表请求，以后代表响应
hello_msgs/Team team
##### 修改上级的 package.xml
# 插入: 
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
##### 打开同级的 CMakeLists.txt
# 在 find_package 函数中加入 message_generation
# 找到 add_service_files 函数并放开注释
# 将 service1.srv, service2.srv 删除，追加 <X>.srv(srv文件夹下的文件的名字)
# 找到 generate_messages 函数并放开注释
# 找到 catkin_package，放开 CATKIN_DEPENDS roscpp rosmsg rospy 并追加 message_runtime
##### 编译
cd <workspace>
catkin_make
```

- 服务端

```Python
# FindTeam.py
from hello.srv import FindTeam, FindTeamRequest, FindTeamResponse

# 参数是请求数据，返回值是响应数据
def service_callback(request):
    name = request.stu.name
    age = request.stu.age
    response = FindTeamResponse()
    team = Team()
    response.team = team
    response.team.name = 'X'
    # 响应结果
    return response

if __name__ == '__main__':
    rospy.init_node('team_server')
    service_name = '/find/team'
    rospy.Service(service_name, FindTeam, service_callback)
    rospy.spin()
```

- 调试 service

`rosservice call <srv_name> <request>`

- 客户端

```Python
import sys  # 如果需要以命令行参数的形式动态传递参数，需要使用 sys.argv
import rospy
from plumbing_server_client.srv import AddInts, AddIntsRequest, AddIntsResponse

if __name__ == '__main__':
    if len(sys.argv) != 3:
        rospy.loginfo('参数必须是2个！')
        sys.exit(1)
    rospy.init_node('erHei')
    # 创建客户端对象
    client = rospy.ServiceProxy('addInts', AddInts)
    # 确保服务端已经启动，否则挂起
    client.wait_for_service()
    # 或者
    # rospy.wait_for_service('<服务使用的 Topic 名>')
    # 发送请求，返回的是一个 response 对象
    response = client.call(int(sys.argv[1]), int(sys.argv[2]))
    rospy.loginfo(response.sum)
```

#### 参数服务器

- `master` 作为托管公共容器
- `talker` 可以向容器设置参数
- `listener` 可以从容器获取参数
- 这些通讯都基于 `RPC`
- 支持的数据类型
    - 32-bit int | booleans | iso8601 dates(时间)
    - lists      | base64   | dicts | strings
- 操作参数

```Python
if __name__ == '__main__':
    rospy.init_node('param_set_p')
    # 设置参数 (键, 值)
    rospy.set_param('name', 'ivy')
    rospy.set_param('age', 18)
    # 删除参数 (key)
    try:
        rospy.delete_param('age')
    except Exception:
        rospy.loginfo('参数不存在')
        
```

- 调试参数
    `rosparam list` 获取所有键
    `rosparam get <key>` 获取某个键的值
- 参数查询
```Python
if __name__ == '__main__':
    rospy.init_node('get_param_p')
    name = rospy.get_param('name', 'hu')  # 如果获取不到参数默认使用 'hu'
    age = rospy.get_param_cached('age', -1)  # 获取数据并且缓存，效率高
    list = rospy.get_param_names()  # 获取包含所有键名的列表
    hasParam = rospy.has_param('name')  # 判断一个键是否存在
    key = rospy.search_param('age')  # 查找键并返回完整键名
```



