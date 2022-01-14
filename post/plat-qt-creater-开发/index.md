# 使用CREATER开发


## 1. Qt Creater配置

1. 【Tools】->【Options】打开选项。

### 1.1. Kits

1. 配置可用的编译工具。

## 2. QtCreater项目

1. .h是头文件；
2. .cpp是源文件；
3. .ui是界面文件。

### 2.1. `.pro`是项目配置文件

```c++
INCLUDEPATH += // 增加头文件的目录，末尾不需要加斜线 
QMAKE_LFLAGS += // 链接标志
QMAKE_CXXFLAGS += //用来给编译器传递开关
LIBS += //用来链接外部的库
```

