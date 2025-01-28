#### 命令

|命令|备注|
|-|-|
|-S .|source，指定CMake工程位置|
|-B 目录|build，指定CMake构建输出路径|
|-G 生成器|generate，生成项目|
|--build 目录|构建项目|
|start 项目|启动一个已生成的项目|

#### 建议的构建方式

```Shell
# 建议将构建输出单独存放到一个文件夹中
mkdir cmake-build-debug
cd cmake-build-debug
cmake ..
make
```

#### 预定义变量

```CMake
# 根级CMakeLists所在的路径
PROJECT_SOURCE_DIR
# 当前CMakeLists所在的路径
CMAKE_CURRENT_SOURCE_DIR
# C++语言编译器编译选项，演示
set(CMAKE_CXX_FLAG "${CMAKE_CXX_FLAG} -std=c++11")
# 设定编译类型
set(CMAKE_BUILD_TYPE Debug) # or Release
```

#### 基本配置

```CMake
# 最低CMake版本
cmake_minimum_required(VERSION 3.26)
# 设置编译器
set(CMAKE_C_COMPILER "C:/Program Files/LLVM/bin")
set(CMAKE_CXX_COMPILER "C:/Program Files/LLVM/bin")
# 声明项目名称，语言等
project(project_name
    LANGUAGES CXX C
    DESCRIPTION "对此项目的描述"
    VERSION 0.1.0
)
# 设置语言标准
set(CMAKE_CXX_STANDARD 11)
# 如果项目基于makefile或者ninja，需要开启如下功能，才能开启语言服务器
set(EXPORT_COMPILE_COMMANDS ON)
```

#### 字符串

```CMake
# 字符串拼接，new_str是str1和str2拼接的结果
set(<new_str> <str1> <str2>)
# 字符串追加，将str1和str2追加到old_str中
list(APPEND ${old_str} str1 str2)
# 字符串移除，将str1和str2从str中移除
list(REMOVE_ITEM ${str} str1 str2)
```

#### 文件搜索

```CMake
# 搜索directory下所有cpp源文件
file(GLOB src <directory>/*.cpp)
# 搜索directory下所有源文件
aux_source_directory(directory src)
```

#### 头文件

```CMake
# 指定头文件目录 
include_directories(./includeA ./includeB)
# 为目标指定头文件目录
target_include_directories(app PRIVATE "./include")
```

#### 库

```CMake
# 指定库输出路径
set(LIBRARY_OUTPUT_PATH <dir>)
# 下面的可以生成动态库，但不能生成静态库
# 因为linux下动态库具有可执行权限，而静态库没有
set(EXECUTABLE_OUTPUT_PATH <dir>)
# 生成库，动态：SHARED，静态：STATIC
add_library(<lib_name> STATLC <cpp_files>)
# 链接库
# 1. 指定库的路径
link_directories(<dir>)
# 2. 链接库
# 2.1链接静态库，可以是libname.lib/libname.a也可以是name
link_libraries(<lib_name>)
# 2.2链接动态库或静态库，libs可以有多个
# target是add_executable或add_library生成的目标
# target_link_libraries要写在文件最后，链接本是最后一步
target_link_directories(target PUBLIC ${PATH})
target_link_libraries(target libs)
# 设置输出路径
set(EXECUTABLE_OUTPUT_PATH <dir>)
# or
set(LIBRARY_OUTPUT_PATH <dir>)
# 输出库
add_executable(lib_name <files.cpp>)
# or
add_libraries(lib_name [SHARED] ${src})
```

#### 其他

```CMake
# 设置父节点，父节点的变量对子节点可见，子目录下需要有一个CMakeLists
add_subdirectory(<子目录>)
# 输出可执行文件
add_executable(<app_name> <cpp_files>)
# 添加宏DEBUG
add_definitions(-DDEBUG)
# 输出消息，注意：这里要加引号，可以内嵌变量
# 不加STAUTS默认被当作一条重要消息
message(STATUS "src: ${src}")
# 设置对所有编译器生效的编译选项
add_compile_options()
# 为一个目标指定编译选项
target_compile_features(app PRIVATE cxx_std_11)
# 编译开关，默认为off
option(OPTION0 "description" OFF)
if (OPTION0) # if中的变量不需要使用${}
    # other scripts...
endif()
# 通过以上操作用户可以在CMake GUI指定一些选项
# 也可以在命令行: cmake -S . -B ./cmake-build -DOPTION0=ON
# 判断工程是不是顶层工程
if (PROJECT_IS_TOP_LEVEL)
# 循环
foreach(var in list)
    message("...")
endforeach()
```
