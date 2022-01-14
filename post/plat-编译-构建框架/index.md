# 构建框架（make）


## 1. 使用`make menuconfig`

### 1.1. [ulfalizer/Kconfiglib](https://github.com/ulfalizer/Kconfiglib)

1. Just drop kconfiglib.py and the scripts you want somewhere. There are no third-party dependencies, but the terminal menuconfig won't work on Windows unless a package like windows-curses is installed.
2. Kconfiglib is a Kconfig implementation in Python 2/3. 

### 1.2. 使用

1. 使用Kconfig语法写一个Kconfig文件。
2. `python3 -m menuconfig/guicongfig/alldefconfig`进行配置，会生成`.config`的中间文件.
3. `python3 -m genconfig`会生成一个头文件（生成的时候，会和老文件进行对比），默认为`config.h`文件.需要在源码中引用。【每次更改完配置都要执行】。

### 1.3. Kconfig语法

1. [KConfig使用介绍](https://blog.csdn.net/qq_23274715/article/details/104880443)

## 2. [CMake](https://cmake.org/)

1. 一些使用 CMake 作为项目架构系统的知名开源项目有 VTK、ITK、KDE、OpenCV、OSG 等。
2. [CMake 入门实战](https://www.hahack.com/codes/cmake/#)

### 2.1. 安装

1. `sudo apt-get install cmake`.

### 2.2. 使用

1. `CMake`的所有的语句都写在一个叫`CMakeLists.txt`的文件中。
2. 当`CMakeLists.txt`文件确定后,可以用`ccmake`命令对相关的变量值进行配置。这个命令必须指向`CMakeLists.txt`所在的目录。
3. 配置完成之后,应用`cmake`命令生成相应的`makefile`（在Unix like系统下）或者 `project`文件（指定用window下的相应编程工具编译时）。
4. **需要使用[此工具](https://github1s.com/Neutree/c_cpp_project_framework/blob/HEAD/tools/kconfig/genconfig.py)获取Kconfig的配置**。

#### 2.2.1. 编写`CMakeLists.txt`

1. "#"后面为注释的内容，CMake的命令`不区分大小写`.

##### 2.2.1.1. 内置变量

```yml
1. CMAKE_BINARY_DIR/PROJECT_BINARY_DIR/<projectname>_BINARY_DIR
    #这三个变量指代的内容是一致的,如果是 in source 编译,指得就是工程顶层目录,如果是 out-of-source 编译,指的是工程编译发生的目录。
    #PROJECT_BINARY_DIR 跟其他指令稍有区别,现在,你可以理解为他们是一致的。
2. CMAKE_SOURCE_DIR/PROJECT_SOURCE_DIR/<projectname>_SOURCE_DIR
    #这三个变量指代的内容是一致的,不论采用何种编译方式,都是工程顶层目录。也就是在 in source 编译时,他跟 CMAKE_BINARY_DIR 等变量一致。
    #PROJECT_SOURCE_DIR 跟其他指令稍有区别,现在,你可以理解为他们是一致的。
3. CMAKE_CURRENT_SOURCE_DIR
    #指的是当前处理的 CMakeLists.txt 所在的路径
4. CMAKE_CURRRENT_BINARY_DIR
    #如果是 in-source 编译,它跟 CMAKE_CURRENT_SOURCE_DIR 一致,如果是 out-of-source 编译,他指的是 target 编译目录。
    #使用我们上面提到的 ADD_SUBDIRECTORY(src bin)可以更改这个变量的值。
    #使用 SET(EXECUTABLE_OUTPUT_PATH <新路径>)并不会对这个变量造成影响,它仅仅修改了最终目标文件存放的路径。
5. CMAKE_CURRENT_LIST_FILE
    #输出调用这个变量的 CMakeLists.txt 的完整路径
6. CMAKE_CURRENT_LIST_LINE
    #输出这个变量所在的行
7. CMAKE_MODULE_PATH
    #这个变量用来定义自己的 cmake 模块所在的路径。如果你的工程比较复杂,有可能会自己编写一些 cmake 模块,这些 cmake 模块是随你的工程发布的,为了让 cmake 在处理CMakeLists.txt 时找到这些模块,你需要通过 SET 指令,将自己的 cmake 模块路径设置一下。
    #比如
    #    SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
    #    这时候你就可以通过 INCLUDE 指令来调用自己的模块了。
8. EXECUTABLE_OUTPUT_PATH 和 LIBRARY_OUTPUT_PATH
    #分别用来重新定义最终结果的存放目录
9. PROJECT_NAME
    #返回通过 PROJECT 指令定义的项目名称。
COMPILE_FLAGS #编译选项
LINK_FLAGS #链接选项
CMAKE_C_FLAGS #
CMAKE_C_FLAGS_DEBUG
CMAKE_C_FLAGS_RELEASE
CMAKE_CXX_FLAGS #
CMAKE_CXX_FLAGS_DEBUG
CMAKE_CXX_FLAGS_RELEASE
CMAKE_SHARED_LINKER_FLAGS #共享库的链接标志
CMAKE_EXE_LINKER_FLAGS #可执行程序的链接标志
```

```yml
使用find_package引入外部依赖包
对于系统预定义的 Find<LibaryName>.cmake 模块，使用方法一般如上例所示。

每一个模块都会定义以下几个变量 -

<LibaryName>_FOUND

<LibaryName>_INCLUDE_DIR or <LibaryName>_INCLUDES <LibaryName>_LIBRARY or <LibaryName>_LIBRARIES
```

##### 2.2.1.2. Cmake文件中数组操作

```yml
# 增
LIST(APPEND <list> <element> [<element> ...]) # 追加，【改变原数组】
LIST(PREPEND <list> [<element> ...]) # 将元素插入到列表的0索引位置
LIST(INSERT <list> <element_index> <element> [<element> ...]) # 在指定位置将元素（一个或多个）插入到列表中
# 删
LIST(POP_BACK <list> [<out-var>...]) # 将列表中最后元素移除，如果指定了输出变量，则会将最后一个元素移入到该变量，并【将元素从原列表中移除】。如果指定了多个输出变量，则依次将原列的最后一个元素移入到输出变量中，如果输出变量个数大于列表的长度，那么超出部分的输出变量未定义。
LIST(POP_FRONT <list> [<out-var>...]) # 将列表中第一个元素移除，其他与上相同
LIST(REMOVE_ITEM <list> <value> [<value> ...]) # 将指定的元素从列表中移除
LIST(REMOVE_AT <list> <index> [<index> ...]) # 将指定索引的元素从列表中移除
LIST(REMOVE_DUPLICATES <list>) # 移除列表中的重复元素
LIST(FILTER <list> <INCLUDE|EXCLUDE> REGEX <regular_expression>) # 根据正则表达式包含或排除列表中的元素，【改变原数组】元素变少
# 改
LIST(TRANSFORM <list> <ACTION> [<SELECTOR>] [OUTPUT_VARIABLE <output variable>]) # 指定的动作运用到所有或者部分指定的元素
# 查
LIST(FIND <list> <value> <output variable>) # 查找，返回索引
LIST(GET <list> <element index> [<element index> ...] <output variable>) # 获取元素（如-1，0，1），存储到新创建的变量中
LIST(JOIN <list> <glue> <output variable>) # 将列表中的元素用<glue>链接起来，组成一个字符串后，返回给<output variable>变量
LIST(LENGTH <list> <output variable>) # 获取数组的长度，存储到新创建的变量中
LIST(SUBLIST <list> <begin> <length> <output variable>) # 获取数组的一部分
# 排序
LIST(REVERSE <list>) # 将整个列表反转
LIST(SORT <list> [COMPARE <compare>] [CASE <case>] [ORDER <order>]) # 排序
```

##### 2.2.1.3. CMakeLists.txt需要的元素

```yml
cmake_minimum_required(VERSION 3.2)     #1. 指定cmake的最低版本，必需
PROJECT(test_math LANGUAGES C CXX)      #2. 指定工程名，一般和项目的文件夹名称对应，必需，指定语言（可选）
message("")                             #3. 用来打印信息
ADD_DEFINITIONS("-Wall -lpthread -g")   #4. 指定编译选项
INCLUDE_DIRECTORIES(include)            #5. 指定【头文件】搜索目录,可以有多个
LINK_DIRECTORIES()                      #   指定【库】搜索目录
link_libraries()                        #   （添加需要链接的库文件路径，注意这里是全路径），该用法【已经被废弃】
#FIND 系列指令，通过FIND寻找路径并进行添加
FIND_PATH(<VAR> name1 path1 path2 ...)  # VAR 变量代表包含这个文件的路径
FIND_FILE(<VAR> name1 path1 path2 ...)  # VAR 变量代表找到的文件全路径，包含文件名
FIND_LIBRARY(<VAR> name1 path1 path2 ...) #VAR 变量表示找到的库全路径，包含库文件名
FIND_PROGRAM(<VAR> name1 path1 path2 ...) #VAR 变量代表包含这个程序的全路径。
FIND_PACKAGE                            # 【重点】
AUX_SOURCE_DIRECTORY(src DIR_SRCS)      #6. 该命令会查找指定目录下的所有源文件，然后将结果存进指定变量名
add_subdirectory(math)                  #7. 添加子目录，这个目录里边也有一个CMakeLists.txt

SET(TEST_MATH  ${DIR_SRCS})             #8. 设置环境变量
SET(LIBRARIES libm.so)                  #9. 设置外部的依赖库
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)  #10. 设置可执行文件的输出路径
SET(FS_BUILD_BINARY_PREFIX "Yfs")
# 加入一个配置头文件 config.h ，这个文件由 CMake 从 config.h.in 生成，通过这样的机制，将可以通过预定义一些参数和变量来控制代码的生成
configure_file (
  "${PROJECT_SOURCE_DIR}/config.h.in"
  "${PROJECT_BINARY_DIR}/config.h"
)
# 是否使用自己的 MathFunctions 库
option (USE_MYMATH  #添加了一个 USE_MYMATH 选项，并且默认值为 ON
       "Use provided math implementation" ON)
# 是否加入 MathFunctions 库
if (USE_MYMATH) #根据 USE_MYMATH 变量的值来决定是否使用我们自己编写的 MathFunctions 库。
  include_directories ("${PROJECT_SOURCE_DIR}/math")
  add_subdirectory (math)  
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

# 生成可执行文件（2选1）
#add executable file
ADD_EXECUTABLE(${FS_BUILD_BINARY_PREFIX}sqrt ${TEST_MATH})  #指定生成目标（运行程序的名称，源文件列表），必需
# 生成链接库（2选1）
add_library (MathFunctions STATIC(SHARED) ${DIR_LIB_SRCS})  #STATIC和SHARED二选一,注自动在库名前增加`lib`，在库名后增加`.so`
#add link library
TARGET_LINK_LIBRARIES(${FS_BUILD_BINARY_PREFIX}sqrt ${LIBRARIES}) #指定需要链接的链接库,【必须】,可以是可执行文件，也可以是库
# 指定安装路径 make install
install (TARGETS Demo DESTINATION bin)  # 目标文件
install (FILES "${PROJECT_BINARY_DIR}/config.h" # 其他文件
         DESTINATION include)
# 启用测试 make test
enable_testing()
# 测试程序是否成功运行
add_test (test_run Demo 5 2)    #第一个测试 test_run 用来测试程序是否成功运行并返回 0 值
# 测试帮助信息是否可以正常提示
add_test (test_usage Demo)
set_tests_properties (test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")

# 测试 5 的平方
add_test (test_5_2 Demo 5 2)

set_tests_properties (test_5_2
 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")

# 测试 10 的 5 次方
add_test (test_10_5 Demo 10 5)

set_tests_properties (test_10_5
 PROPERTIES PASS_REGULAR_EXPRESSION "is 100000")

# 测试 2 的 10 次方
add_test (test_2_10 Demo 2 10)

set_tests_properties (test_2_10
 PROPERTIES PASS_REGULAR_EXPRESSION "is 1024")  # PASS_REGULAR_EXPRESSION 用来测试输出是否包含后面跟着的字符串。
```

1. config.h.in

```yml
#cmakedefine USE_MYMATH    #（对应C/C++文件中的宏）include "config.h"
```

#### 2.2.2. 命令行选项

##### 2.2.2.1. 环境变量

1. `CMAKE_INCLUDE_PATH` 头文件搜索路径，分号;你不使用 FIND_PATH，CMAKE_INCLUDE_PATH 变量的设置是没有作用的.
2. `CMAKE_LIBRARY_PATH` 库文件搜索路径，分号;CMAKE_LIBRARY_PATH 可以用在 FIND_LIBRARY 中。.

##### 2.2.2.2. ccmake

1. `..`可以查看修改预定义的值。

##### 2.2.2.3. cmake

1. ‵-H.`在当前目录中搜索CMakeLists.txt文件。
2. `-Bbuild`在build目录中生成所有的文件，方便删除缓存。
3. `--build .`类似于直接执行`make`命令。
4. `-D`定义一个宏。

##### 2.2.2.4. 使用交叉编译

1. 编写`CrossCompile.cmake`文件,可供多个程序使用：

    ```yml
    set(CMAKE_SYSTEM_NAME Linux)                                # 必须
    set(TOOLCHAIN_PATH /OPT/gcc-arm-linux-gnueabi)
    set(CMAKE_C_COMPILER ${tools}/bin/arm-linux-gnueabi-gcc)    # 交叉编译工具
    set(CMAKE_CXX_COMPILER ${tools}/bin/arm-linux-gnueabi-g++)  # 交叉编译工具

    # where is the target environment 
    SET(CMAKE_FIND_ROOT_PATH  /opt/arm/ppc_74xx /home/rickk/arm_inst)  # 指定目标环境，一系列的路径

    # search for programs in the build host directories (not necessary)
    SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
    # for libraries and headers in the target directories
    SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)  #只会
    SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

    # configure Boost and Qt
    SET(QT_QMAKE_EXECUTABLE /opt/qt-embedded/qmake)
    SET(BOOST_ROOT /opt/boost_arm)
    ```

2. 上述命令必须写入脚本中，使用 -DCMAKE_TOOLCHAIN_FILE=xxx.cmake 的方式使用。不能直接写入 CMakeLists.txt 或使用 include(xx.cmake)。

## 3. 示例

### 3.1. [Neutree/c_cpp_project_framework](https://github.com/Neutree/c_cpp_project_framework/blob/master/README_ZH.md)

1. 一个足够简单易用并且可配置的用于构建`C/C++`的模板工程;
2. 使用`CMake`构建，并且支持带GUI配置界面的`Kconfig`.

### 3.2. [kracejic/cleanCppProject](https://github1s.com/kracejic/cleanCppProject)

1. Features: Modern CMake, CPack, Doxygen, PlantUML, Catch Unit testing, static analysis.

## 4. 总结（适合自己的构建系统）

### 4.1. 目录结构

1. 参考ESP32的目录组织。

    ```shell
    CMakeLists.txt
    Kconfig
    - components/ #组件(一般每个组件编译为一个静态库)
        - component1/ 
            - CMakeLists.txt
            - Kconfig
            - src/
                - src1.c
            - inc/
        - component2/ 
            - CMakeLists.txt
            - Kconfig
            - src/
                - src1.c
            - inc/ 
                - component2.h
    main    #生成可执行文件的主要代码
        - CMakeLists.txt    # 组件规则
        - src
        - inc
    build   #生成结果目录
    cmake/  #自己写的cmake模块
    tools/  #其他工具，比如读取kconfig的配置
    ```

2. 欢迎使用[模板](https://gitee.com/heartblood/c-c-project-template)

