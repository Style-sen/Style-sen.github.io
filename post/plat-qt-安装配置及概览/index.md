# QT安装配置及概览


1. Qt的实现主要是采用p-impl手法，实现接口与实现分离，它有很好的消息循环机制，有的对象与线程的相关性，它也有借助moc生成反射元信息，这种设计方法至今仍然非常适用。
2. Qt内核部分其实是完全和界面无关的，你完全可以抛开Qt GUI部分来学习和使用Qt。当你完完全全学习了qt及其理念后，你会发现写优雅的代码是一件十分简单的事情，它可以应用到你之后的所有过程中去。

## 1. 开发方式

1. 如果开发的程序只管windows上跑，不用跨平台，也不关心是否需要使用Qt Creator打开，那么推荐使用VS+Qt方式进行开发。把Qt当成一个界面库来在VS中调用。VS编码+MSVC编译。
2. 需要考虑跨平台，那么最好不要使用VS+Qt这种方式。纯QtCreator方式，QtCreator编码+MinGW或MSVC或其他编译。

## 2. 软件架构

1. 原则：UI界面和业务逻辑需要尽可能的分离（判断一个结构的解耦程度，一个简单的办法是离开了UI界面，业务逻辑是否可以正常调用和运行，如果可以，说明这个架构是比较成功的。同时，对UI界面和业务逻辑功能的每个模块，是否能够被替换，而不影响整个项目的功能，这点也是判断架构解耦性的一个指标）。两者的链接用信号与槽机制。然后每个业务逻辑按照功能划分，封装成一个单独的函数或者类。
2. 三层： UI层（所有的主界面都会从这三个类：QWiget,QMainWindow,QDialog中的一个继承而来，并在main函数里生成实例并show出来，进入主消息循环。可以使用QWiget，不使用UI文件，所有的界面都new出来，再使用代码来布局）；控制器层(建线程，实例化业务逻辑类，搭建信号和槽)；模型层(业务逻辑，在线程中执行)。

    ```shell
    三层：
    UI界面层（view）——>控制器层：当用户操作UI界面时，发射一个控制器层【信号】；
    控制器层（controller）——>模型层：控制层【调用】模型层功能函数，实现对应业务逻辑功能；
    模型层（model）——>控制器层：模型功能层，完成业务逻辑后，再发射一个控制器层【信号】，声明完成了该业务逻辑功能。
    控制器层（controller）——>UI界面层：控制器层接收到该支线程完成了对应的业务逻辑，开启槽函数结束该支线程，然后发射一个控制器层完成业务逻辑【信号】到UI界面层。UI界面层收到 信号，显示对应的结果UI界面。
    
    注意：整个过程控制器有【三个关键信号】：启动业务逻辑功能信号；退出该业务逻辑线程信号；完成业务逻辑信号。而model层功能函数是用【支线程】来执行。也就是说，所有的模型功能函数 都是用【支线程】完成，这样可以保证UI界面的流畅度。
    ```

3. 基于上边3层，还可以增加一个通讯层，用于调用通信接口逻辑。

### 2.1. 基于插件的架构

1. 【注】以前基于APP+DLL的传统架构，现在流行插件架构（核心加插件组件）：【方便功能的扩展（接口定义好）】；【更新量小（只更新插件）】；【降低模块之间依赖】；【面向未来（API稳定）】。
2. 分为三部分：

    ```markdown
    主系统:通过插件管理器加载插件，并创建插件对象。一旦插件对象被创建，主系统就会获得相应的指针/引用，它可以像任何其他对象一样使用。
    插件管理器:用于管理插件的生命周期，并将其暴露给主系统。它负责查找并加载插件，初始化它们，并且能够进行卸载。它还应该让主系统迭代加载的插件或注册的插件对象,很难与主系统分割干净。
    插件:插件本身应符合插件管理器协议，并提供符合主系统期望的对象。
    ```

3. 开源项目[【开源】基于Qt的跨平台插件开发框架QCPFrame（一）](https://blog.csdn.net/jam12315/article/details/108461027)

## 3. 国内源

中国科学技术大学：<http://mirrors.ustc.edu.cn/qtproject/>
清华大学：<https://mirrors.tuna.tsinghua.edu.cn/qt/>
北京理工大学：<http://mirror.bit.edu.cn/qtproject/>
中国互联网络信息中心：<http://mirror.bit.edu.cn/qtproject/>

注:

   1. 5.15开始，官方不再提供编译好的版本。
   2. 6.0开始，貌似只支持win10以上的windows。

## 4. 官方安装包安装(只支持到5.12.12LTS)

1. 下载`.run`文件。
2. 增加执行权限。
3. 运行，选择所有模块（包括qtcreater和qmake）。

## 5. 编译源码(5.15.2)

1. 下载`*everywhere*`源码，可以使用编译工具，编译到各个平台（Windows，Linux，Arm）。
2. [Building Qt Sources](https://doc.qt.io/qt-5/build-sources.html).

### 5.1. Ubuntu

1. 下载`qt-everywhere-src-5.15.2.tar.xz`.
2. 解压`tar xvJf qt-everywhere-src-5.15.2.tar.xz`。
3. 安装依赖(可以不安装依赖，直接走下一步，会没有一些模块)

    ```shell
    sudo apt-get install gperf # WARNING: gperf is required to build QWebEngine.
    sudo apt-get install flex # WARNING: flex is required to build QWebEngine.
    sudo apt-get install bison # WARNING: bison is required to build QWebEngine.
    ```

4. 执行`configure`

    ```shell
    mkdir qt-5.15.2-build
    cd qt-5.15.2-build
    ./../qt-everywhere-src-5.15.2/configure -release -prefix /opt/qt-5.15.2
    ```

5. 执行`make`(大概会需要半小时到4小时不等)
6. 【注】不需要`make install`.

### 5.2. windows

1. 编译之前[Qt for Windows - Requirements](https://doc.qt.io/qt-5/windows-requirements.html).
2. 参考[Qt for Windows - Building from Source](https://doc.qt.io/qt-5/windows-building.html)
3. 参考[在Windows环境下编译Qt 5.15.1](https://tanjoe.github.io/2020/09/21/%E5%9C%A8Windows%E7%8E%AF%E5%A2%83%E4%B8%8B%E7%BC%96%E8%AF%91Qt-5-15-1/)

    ```shell
    1. 安装python；
    2. 安装perl；
    3. 安装openssl或其他可选依赖。
    4. 注意configure的时候使用-debug-and-release选项

    configure.bat 
    -static #指明是静态编译
    -prefix "D:\qt" #指明安装的目录
    -confirm-license -opensource  #指明是开源版本的qt
    -debug-and-release #指明需要debug版和release版，可以单独选择release版
    -platform win32-msvc  #指明使用msvc编译，这里的win32并不指32位
    -nomake examples -nomake tests  #不编译样例
    -plugin-sql-sqlite -plugin-sql-odbc -qt-zlib -qt-libpng -qt-libjpeg #可选插件
    -opengl desktop  #用系统自带的opengl
    -mp #多核编译
    ```

4. 安装完毕之后，将bin目录放进Path中。

## 6. Qt多版本管理

### 6.1. qtchooser

## 7. 理论支持

1. QT实质上是用C++编写的跨平台的开发类库。为GUI应用程序和非GUI应用程序提供了各种类。

### 7.1. 元对象系统

1. 元对象系统由3个部分组成。

```c++
1. QObject是所有使用元对象系统的类的基类；
2. 在一个类的private部分声明Q_OBJECT宏，使得类可以使用元对象的特性，如动态属性、信号与槽；
3. MOC（元对象编译器）为每个QObject的子类提供必要的代码来实现元对象系统的特性。是一个预处理器，在源程序被编译前先将Qt特性的程序（发现类的定义里有Q_OBJECT宏）转换为标准C++兼容的形式，然后再由标准C++编译器进行编译。
```

### 7.2. QT全局定义

### 7.3. 容器类

### 7.4. 模块（文件夹）

#### 7.4.1. 基本模块

1. `在所有的开发平台和目标平台都可用`。

##### 7.4.1.1. QT Webenginewidgets

1. `QT += webenginewidgets`
2. 需要将`QtWebEngineProcess.exe`放到exe同级目录，会生成`debug.log`日志文件。

    ```c++
    //QtWebEngineProcess.exe的依赖
    QT5WEBENGINECORED.DLL
    QT5CORED.DLL
    MSVCP120D.DLL
    MSVCR120D.DLL
    ```

3. 需要拷贝resources和tranlations`下`的文件到运行目录。【必要的时候，需要将resources中文件拷贝到运行目录，qtwebengine_locales拷贝到运行目录】。
4. [需要编译安装ICU](https://wiki.qt.io/Compiling-ICU-with-MSVC#Compiling_ICU_with_MSVC_.28Microsoft_Visual_Studio.29),可以下载[编译好的](https://www.npcglib.org/~stathis/blog/precompiled-icu-past/)。

## 8. 工具

1. `Qt Creator`是Qt的IDE，功能最强大，它大大简化了Qt开发，可以原生创建QT应用程序（带有Qt引擎的C++）。并且允许我们创建和编辑源代码，调试应用程序等。集成下边的两个工具：

    ```markdown
    1. `Qt Designer`是用于使用Qt小部件文件（.ui）设计和构建图形用户界面（GUI）的Qt工具，可以按所见即所得（WYSIWYG）的方式编写和自定义窗口或对话框，并使 用不同的样式和分辨率对其进行测试。
    2. `Qt Quick Designer`允许图形化编辑QML文件（.qml），但是现在它已集成在Qt Creator中。
    ```

2. `Qt Design Studio`是一款UI设计和开发工具，它让设计师和开发者可以迅速设计原型，并且开发复杂的可伸缩的UI。简单概括其功能就是`让UI设计转换为qml`，为工程师所用，并且可以与Photoshop集成。

## 9. [有什么很好的软件是用 Qt 编写的](https://www.zhihu.com/question/19630324)

1. [QtCreater](https://github.com/qt-creator/qt-creator) 插件架构，过于复杂。
2. [sqlitestudio](https://github.com/pawelsalawa/sqlitestudio) 也有插件，可以作为入门。

## 10. 解决方法

1. [QSqlDatabasePrivate::addDatabase: duplicate connection name 'qt_sql_default_connection'解决办法](https://blog.csdn.net/simmerlee/article/details/37872793)

