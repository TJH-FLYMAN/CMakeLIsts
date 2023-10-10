#set(EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/build/)  #设置输出的中间文件保存路径
1.指定cmake最小版本
cmake_minimum_required(VERSION 3.4.1)  #指定 cmake最小版本要求为3.4.1

# 本CMakeLists.txt的project名称
# 会自动创建两个变量，PROJECT_SOURCE_DIR和PROJECT_NAME 
# ${PROJECT_SOURCE_DIR}：本CMakeLists.txt所在的文件夹路径
# ${PROJECT_NAME}：本CMakeLists.txt的project名称
# CMAKE_BINART_DIR, PROJECT_BINARY_DIR, <projectName>_BINARY_DIR：这三个变量的含义一样。
2.设置项目名
project(xxx)   比如 project(myProject)


3.设置编译类型
# 将.cpp/.c/.cc文件生成可执行文件
add_executable(main main.cpp)

如果不加STATIC/SHARED,默认为静态库。此外，生成的库默认加前缀lib以及后缀*.a | *.so
# 注意，库文件名称通常为libxxx.so，在这里只要写xxx即可
# 生成静态库(默认) 
add_library(util STATIC util.cpp tools.cpp)  小写是static
# 生成动态库
add_library(util SHARED util.cpp tools.cpp)  小写是shared


4.搜索所有cpp文件
# 获取路径下所有的.cpp/.c/.cc文件，并赋值给变量中
aux_source_directory(路径 变量)  比如 aux_source_directory(/home/tjh/ SRC_LIST)

# 给文件名/路径名或其他字符串起别名，用${变量}获取变量内容
set(变量 文件名/路径/...)  比如set(HOME /home/tjh/)  使用的时候${HOME}

5.添加编译选项 
功能和C/C++中的 #define 是一样的
add_definitions(编译选项)  比如add_definitions(-DTEST_DEBUG)
在代码中就可以使用#ifdef TEST_DEBUG ...#else ...  #endif

6.打印消息
message(消息)

7.复杂项目，子项目，编译子文件夹的CMakeLists.txt
add_subdirectory(路径) 

8.规定头文件,库文件路径
# 规定.h头文件路径，设置全局的头文件路径
include_directories(路径)

# 设置目标的头文件搜索路径，只对target起作用
# target_include_directories(target_name PUBLIC include_dir)

# 规定全局的.so/.a库文件路径
link_directories(路径)

# 链接目标所需的库，只对target起作用
target_link_libraries(可执行文件名称 链接的库文件名称)
# target_link_libraries(target_name lib1 lib2 ...)
# 如果是链接的动态库,则运行的时候需要把它拷贝到可执行文件名称同目录下

9.常用的预定义变量
PROJECT_SOURCE_DIR		工程根目录
PROJECT_BINARY_DIR		运行cmake命令的目录，通常是${PROJECT_SOURCE_DIR}/build
PROJECT_NAME			返回通过project命令定义的项目名称
CMAKE_CURRENT_SOURCE_DIR	当前处理的CMakeLists.txt所在的路径
CMAKE_CURRENT_BINARY_DIR	target 编译目录
CMAKE_CURRENT_LIST_DIR		CMakeLists.txt的完整路径
CMAKE_CURRENT_LIST_LINE	当前所在的行
CMAKE_MODULE_PATH		定义自己cmake模块所在的路径。SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)，然后可以用INCLUDE命令来调用自己的模块
EXECUTABLE_OUTPUT_PATH		重新定义目标二进制可执行文件的存放位置
LIBRARY_OUTPUT_PATH		重新定义目标链接库的存放位置

10.find_package()
find_package(OpenCV REQUIRED)
target_include_directories(myapp PRIVATE ${OpenCV_INCLUDE_DIRS})
target_link_libraries(myapp PRIVATE ${OpenCV_LIBS})

