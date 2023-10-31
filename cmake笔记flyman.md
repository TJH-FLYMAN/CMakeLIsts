# Cmake
[Cmake官方网站](www.cmake.org)
## Cmake概述
 1. 项目构建工具
 2. 兼容不同平台（跨平台），makefile依赖当前编译平台，且编写工作量大，Cmake会根据编译平台，自动生成本地化的Makefile和工程文件，make编译即可。
 3. 一键生成Makefile的工具
## Cmake优点
1. 跨平台
2. 能够管理大型项目 
3. 简化编译构建过程和编译过程 
4. 可扩展：可以为 cmake 编写特定功能的模块，扩充 cmake 功能
## Cmake语法
1. Cmake文件名称 **CMakeLists.txt**
2. Cmake使用`#`注释或者`#[[ ]]`，前者一行，后者多行
3. `cmake_minimum_required()` 
指定使用cmake的最低版本
4. 变量的使用`${}`取值，但是在 IF 控制语句中是直接使用变量名
5.  指令(参数 1 参数 2...) 参数使用括弧括起，参数之间使用空格或分号分开。 以ADD_EXECUTABLE 指令为例，如果存在两个cpp源文件
```cpp
 ADD_EXECUTABLE(hello A.cpp B.cpp)
 ADD_EXECUTABLE(hello A.cpp;B.cpp)
```
6. 指令和大小写无关，参数和变量是大小写相关。推荐全部使用大小写
7. 1
### demo
```cpp
//文件目录
[root@*]# tree
├── build
├── CMakeLists.txt
└── src
	└── hello.cpp
```
以hello.cpp为例
```cpp
#include <iostream>
using namespace std;
auto main()->int{
	cout<<"hello"<<endl;
}
```
**CMakeLists.txt**
```cpp
cmake_minimum_required (VERSION 3.0)
PROJECT (HELLO)
SET(SRC_LIST hello.cpp)
MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})
MESSAGE(STATUS "This is SOURCE dir "${HELLO_SOURCE_DIR})
ADD_EXECUTABLE(hello ${SRC_LIST})
```
`cmake .`输出结果
```cpp
-- The C compiler identification is GNU 9.4.0
-- The CXX compiler identification is GNU 9.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- This is BINARY dir /home/tjh/cmake/hello/build
-- This is SOURCE dir /home/tjh/cmake/hello
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tjh/cmake/hello/build

```
`make`输出结果
```cpp
Scanning dependencies of target hello
[ 50%] Building CXX object CMakeFiles/hello.dir/src/hello.cpp.o
[100%] Linking CXX executable hello
[100%] Built target hello
```
生成了可执行文件hello
### PROJECT
定义工程名称（必须），也可指定工程版本、描述语言等（可选）
```cpp
PROJECT(HELLO) //指定工程名字为HELLOWORLD,支持所有语言
PROJECT(HELLO CXX) //指定工程名字为HELLOWORLD,支持C++
PROJECT(HELLO C CXX) //指定工程名字为HELLOWORLD,支持C和C++
```
在定义了`PROJECT`时会自动定义两个`CMAKE`变量，两个都指向当前工作目录
```cpp
<project_name>_BINARY_DIR，本例中是 HELLO_BINARY_DIR（编译路径）
<project_name>_SOURCE_DIR，本例中是 HELLO_SOURCE_DIR（工程路径）
如果改了工程名，这两个变量名也会改变
所以Cmake定义了两个预定义变量PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR，两变量和上述一致
PROJECT_BINARY_DIR = HELLO_BINARY_DIR
PROJECT_SOURCE_DIR = HELLO_SOURCE_DIR
```
### SET
- 用于显式指定变量
```cpp
SET(SRC_LIST hello.cpp) //SRC_LIST变量就包含了hello.cpp
也可 SET(SRC_LIST “main.cpp”)，如果源⽂件名中含有空格，要加双引号
SET多个cpp文件
//方式一：
SET(SRC_LIST hello.cpp A.cpp B.cpp)
//方式二：
SET(SRC_LIST hello.cpp;A.cpp;B.cpp)
```
- 用于指定c++版本
Cmake预定义了宏CMAKE_CXX_STANDARD变量，默认是98
```cpp
//增加-std=c++11编译选项
SET(CMAKE_CXX_STANDARD 11)
//增加-std=c++14编译选项
SET(CMAKE_CXX_STANDARD 14)
//增加-std=c++17编译选项
SET(CMAKE_CXX_STANDARD 17)
```
- 用于指定输出路径
Cmake预定义了宏EXECUATABLE_OUTPUT_PATH变量，默认是当前工作目录
```cpp
//设置输出路径为build/bin，目录不存在会自动创建
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
```
### 搜索文件
- Cmake使用`aux_source_directory()`搜索指定目录下的所有源文件，并保存到变量名中
```cpp
/*
dir: 指定搜索的目录
vairable: 保存搜索结果到变量名
*/
aux_source_directory(dir variable)
```
- 搜索文件命令file()
```cpp
/*
GLOB：搜索指定目录
GLOB_RECURSE：搜索指定目录和子目录。递归搜索
*/
file(GLOB/GLOB_RECURSE variable 要搜索的文件路径和文件类型)
例如
file(GLOB SRC_LIST *.cpp) //搜索当前目录下的所有.cpp文件，并保存到SRC_LIST变量中
file(GLOB_RECURSE SRC_LIST *.cpp) //搜索当前目录下的所有.cpp文件，包括子目录，并保存到SRC_LIST变量中
file(GLOB_RECURSE SRC_LIST *.cpp *.h) //搜索当前目录下的所有.cpp和.h文件，包括子目录，并保存到SRC_LIST变量中
file(GLOB SRC_LIST ${PROJECT_SOURCE_DIR}/ *.cpp) //搜索当前目录下的所有.cpp文件，并保存到SRC_LIST变量中
```
### MESSAGE
向终端输出自定义的信息
```cpp
/*
SEND_ERROR，产⽣错误，⽣成过程被跳过。
SATUS，输出前缀为—的信息。
FATAL_ERROR，⽴即终⽌所有 cmake 过程.
*/
MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] "message to display"...)
```
### ADD_EXECUTABLE关键字
生成可执行文件，`ADD_EXECUTABLE(hello ${SRC_LIST})` 生成的可执行文件名是helloworld，源文件读取变量SRC_LIST中的内容
工程名HELLO和可执行文件hello没关系
- 编译单个文件
```cpp
PROJECT(HELLO)
ADD_EXECUTABLE(hello main.cpp)
```
- 编译多个文件
```cpp
//方式一:
ADD_EXECUTABLE(hello hello.cpp A.cpp B.cpp)
//方式二:
ADD_EXECUTABLE(hello hello.cpp;A.cpp;B.cpp)
```
### ADD_SUBDIRECTORY关键字
用于向当前工程添加存放源文件的子目录，并可以指定中间二进制（中间文件）和目标二进制（make结果/编译结果）存放的位置
```cpp
//文件目录
[root@localhost cmake]# tree
├── build
├── CMakeLists.txt
└── src
	├── CMakeLists.txt
	└── hello.cpp
```
外层CMakeLists.txt
```cpp
cmake_minimum_required (VERSION 3.0)
PROJECT(HELLO)
ADD_SUBDIRECTORY(src bin)
MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})
MESSAGE(STATUS "This is SOURCE dir " ${HELLO_SOURCE_DIR})
```
src下的CMakeLists.txt
```cpp
ADD_EXECUTABLE(hello hello.cpp)
```
build目录下`cmake ..`结果
```
-- The C compiler identification is GNU 9.4.0
-- The CXX compiler identification is GNU 9.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- This is BINARY dir /home/tjh/cmake/hello/build
-- This is SOURCE dir /home/tjh/cmake/hello
-- Configuring done
-- Generating done
-- Build files have been written to: /home/tjh/cmake/hello/build
```
ADD_SUBDIRECTORY使用
```cpp
/*
EXCLUDE_FROM_ALL：⽬录从编译中排除
source_dir ：⼦⽬录加⼊⼯程并指定编译输出
[binary_dir]：子目录编译中间结果路径为，如果未指定，则保存在build/src 路径下
*/
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```
Cmake指定了最终二进制文件保存路径的预定义变量
分别是`EXECUTABLE_OUTPUT_PATH` 和 `LIBRARY_OUTPUT_PATH`

### 安装
- 一种是代码编译后直接`make install` 安装
- 一种是是打包时的指定⽬录安装。`make install DESTDIR=/tmp/test`
安装使用CMAKE指令：`INSTALL`
`INSTALL`的安装可以包括：二进制、动态库、静态库以及文件、目录、脚本等
```cpp
/*
FILES：⽂件
COPYRIGHT：
README：
DESTINATION：安装路径。可以是绝对路径，也可以是相对路径。其中相对路径实际路径是：${CMAKE_INSTALL_PREFIX}/<DESTINATION 定义的路径>
CMAKE_INSTALL_PREFIX 默认是在 /usr/local/
*/
INSTALL(FILES * DESTINATION share/doc/cmake/)
```
### 构建静态库和动态库
在某些情况源代码不需要编译成可执行程序，而是生成静态库或动态库提供给第三方使用
Cmake提供`add_library`指令建立一个静态库和动态库，提供函数供其他程序编程使用
- 构建静态库
```cpp
/*
hello ：库名
STATIC ： 编译静态库STATIC，编译动态库SHARED（参数）
${LIBHELLO_SRC}：源文件 
*/
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})
```
在linux中静态库名字一般为lib+库名+.a，在这里生成的库名是libhello.a(a=archive,档案)。
- 构建动态库
```cpp
/*
hello ：库名
SHARED ： 编译动态库SHARED（参数）
${LIBHELLO_SRC}：源文件 
*/
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
```
在Linux中，动态库名字为lib+库名+.so,在这里生成的库名是libhello.so( so=shared object)
- 同时构建静态库、动态库
```cpp
// 如果用这种方式，只会构建一个动态库，不会构建出静态库，虽然静态库的后缀是.a 
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC}) 
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC}) 
// 修改静态库的名字，这样是可以的，但是我们往往希望他们的名字是相同的，只是后缀不同而已 
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC}) 
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
```
- 指定输出库的路径
1. 对于动态库,linux默认有执行权限,所以可以按照生成可执行程序的方式去指定它生成的目录,也就是指定`EXECUTABLE_OUTPUT_PATH`
2. 对于静态库,linux默认没有执行权限,所以指定`LIBRARY_OUTPUT_PATH`,此宏适用于静态库文件和动态库文件
- 静态库和动态库的区别
1. 静态库加载速度快,但是相同的库文件数据可能在内存中被加载多份, 消耗系统资源，浪费内存.
2. 静态库文件更新需要重新编译项目文件, 生成新的可执行程序, 浪费时间
3. 静态库被打包到应用程序中加载速度快, 不会影响系统的运行
4. 动态库可实现不同进程间的资源共享,且升级简单, 只需要替换库文件, 无需重新编译应用程序
5. 加载速度比静态库慢, 以现在计算机的性能可以忽略,发布程序需要提供依赖的动态库.
### SET_TARGET_PROPERTIES
可以使用指令`SET_TARGET_PROPERTIES`来设置输出的名称，对于动态库，还可以用来指定动态库版本和 API 版本
```cpp
SET(LIBHELLO_SRC hello.cpp) 
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC}) 
//对hello_static的重名为hello 
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello") //cmake 在构建一个新的target 时，会尝试清理掉其他使用这个名字的库，因为，在构建 libhello.so 时， 就会清理掉 libhello.a 
SET_TARGET_PROPERTIES(hello_static PROPERTIES CLEAN_DIRECT_OUTPUT 1) 
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC}) SET_TARGET_PROPERTIES(hello PROPERTIES OUTPUT_NAME "hello") SET_TARGET_PROPERTIES(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
```
### 指定头文件路径
编译源文件时，需要指定头文件的路径,保证编译过程中编译器能够找到头文件,可以用于指定一个或多个头文件路径
1. include_directories(header-dir ) 是一个全局包含，向下传递。当前目录的 CMakeLists.txt 中使用了该指令，其下所有的子目录默认也包含了header-dir目录
2. target_include_directories(target-name PUBLIC/PRIVATE path) 是一个局部包含，只对指定target有效。
- 指定头文件
```cpp
/*
head_file_path：头文件所在路径
*/
include_directories(head_file_path)
```
- 特定目标指定头文件
指定target编译时使用的头文件路径，使用 `target_include_directories` 命令
```cpp
/*
target_name：
PUBLIC：表示头文件路径是公开的，可以被其他target使用
PRIVATE：表示头文件路径是私有的，只对当前target有效
path：头文件路径
*/
target_include_directories(target_name PUBLIC/PRIVATE path)
```
### 指定链接库路径
编译源文件时，需要指定链接库的路径,保证编译过程中编译器能够找到链接库  
find_package通常用于查找较大的软件包,批量引入头文件和库文件 
find_library通常用于查找特定库文件或者是单个库文件,比如opencv中的单个库文件
- 查找安装在系统上的库
cmake使用`find_package`命令在系统上查找库，如果找到，值`<LIB>_FOUND`就会被赋为TRUE
```cpp
/*
LIB ：库名 
REQURIED/OPTIONAL：表示是否必须找到库 
REQUIRED：必须找到该库，找不到就报错
OPTIONAL：可以找到该库，找不到也不会报错
COMPONENTS：表示要查找的组件，如果库有多个组件，可以指定查找哪个组件
*/
find_package(LIB REQURIED/OPTIONAL)
找到库后，cmake会设置一些变量：
LIB_INCLUDE_DIRS：库头文件路径 , LIB_LIBRARIES：库文件路径
```

以opencv为例,在CMakeLists.txt中,添加find_package(OpenCV REQUIRED)
```cpp
MESSAGE(STATUS "OpenCV_FOUND:----" ${OpenCV_FOUND})
MESSAGE(STATUS "OpenCV_INCLUDE_DIRS:-----" ${OpenCV_INCLUDE_DIRS})
MESSAGE(STATUS "OpenCV_LIBS:----" ${OpenCV_LIBS})
//cmake ..输出结果
-- OPENCV OpenCV_FOUND:----1
-- OPENCV OpenCV_INCLUDE_DIRS:-----/usr/local/include/opencv4
-- VOpenCV_LIBS:-----opencv_calib3dopencv_coreopencv_dnnopencv_features2d ........(后面省略)
```
- 查找特定库所在路径
cmake使用`find_library`在目录中查找，如果所有目录中都没有，值RUNTIME_LIB就会被赋为NO_DEFAULT_PATH
```cpp
/*
variable：变量名
name：库名
path：指定查找的路径
*/
find_library(variable name path)
```
以opencv为例,在CMakeLists.txt中,添加FIND_LIBRARY(OPENCV_CORE_LIBRARY opencv_core)
```cpp
MESSAGE(STATUS "opencv_core_FOUND:----" ${OPENCV_CORE_LIBRARY})
//cmake ..输出结果
-- opencv_core_FOUND:----/usr/lib/x86_64-linux-gnu/libopencv_core.so
```
- 链接库
Cmake使用`link_libraries`将具体的库文件引入到当前工程中(具体库文件)
```cpp
/*
target_name：目标名
link_file_path：链接库所在路径
*/
link_libraries(link_file_path)
target_link_libraries(target_name link_file_path)
```
- 指定链接库的查找路径
Cmake使用`link_directories`引入库目录,添加库文件的搜索路径（整个库目录）
```cpp
/*
target-name：库名
*/
link_directory( path1 path2 ...) 
target_link_directory(target-name PUBLIC/PRIVATE path)
```
### 自定义宏
cmake使用`add_definitions`命令向C/C++编译器添加预定义的宏定义
```cpp
/*
DEFINE: 定义宏名
-D：定义   -U：取消定义
*/
add_definitions(-D<DEFINE>)
```
### 指定目标依赖关系
cmake使用`add_dependencies`命令向C/C++编译器添加预定义的宏定义
```cpp
/*
target：目标名
depenency1：依赖目标名
*/
add_dependencies(target dependency1 dependency2 ...)
```

## 常用预定义变量
- PROJECT_SOURCE_DIR		
工程根目录 
- PROJECT_BINARY_DIR		
运行cmake命令的目录，通常是`${PROJECT_SOURCE_DIR}/build` 
- PROJECT_NAME			
返回通过project命令定义的项目名称 
- CMAKE_CURRENT_SOURCE_DIR	
当前处理的CMakeLists.txt所在的路径 
- CMAKE_CURRENT_BINARY_DIR	
target 编译目录 
- CMAKE_CURRENT_LIST_DIR		
CMakeLists.txt的完整路径 
- CMAKE_CURRENT_LIST_LINE	
当前所在的行 
- CMAKE_MODULE_PATH		
定义自己cmake模块所在的路径。`SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)`，然后可以用INCLUDE命令来调用自己的模块 
- EXECUTABLE_OUTPUT_PATH		
重新定义目标二进制可执行文件的存放位置 
- LIBRARY_OUTPUT_PATH	
重新定义目标链接库的存放位置

## 嵌套CMakeLists.txt
待定
