# Visual Studio


## 1. 支持QT开发

1. 下载并安装[Qt Visual Studio Tools](https://download.qt.io/official_releases/vsaddin/2.7.1/).
2. 在VS的[扩展]中增加QT版本即选中qt安装目录下的qmake.exe。

### 1.1. 设置

#### 1.1.1. Qt

##### 1.1.1.1. General

1. Qt Designer:Run in detached window：true。 允许在新的窗口打开。

## 2. 导入老项目

1. 打开老的解决方案，会提示`重定向解决方案`，选择最新的生成工具。

## 3. 使用

### 3.1. 新建项目

1. 【win32 项目】用于创建Win32应用程序、控制台应用程序、DLL或其他静态库的项目。
2. 【生成的动态链接库并不能直接修改库文件的名称，而是需要重新编译】。

### 3.2. 配置项目

#### 3.2.1. 项目配置文件`*.vcxproj`

```xml
<Keyword></Keyword> # vs2013+qt5.6+addin1.2.5 Qt4VSv1.0 就转变为为qt项目 
```

#### 3.2.2. 用户配置文件`*.vcxproj.user`

```xml
<QTDIR><QTDIR>
```

#### 3.2.3. 项目属性图形化配置

1. 我们一般不会修改解决方案的属性，而是设置每个项目各自的属性.
2. 一个项目有release和debug两套【独立】属性。

    ```c++
    //1.Debug 通常称为调试版本，它包含调试信息，并且不作任何优化，便于程序员调试程序。
    //2.Release 称为发布版本，它往往是进行了各种优化，使得程序在代码大小和运行速度上都是最优的，以便用户很好地使用。
    ```

3. 路径推荐使用编译器提供给我们的宏变量，而尽量不要使用绝对的名称，这样程序更具有移植性。
4. 如果有某些项目是作为导出链接库用的。需要把导出的dll, lib（即输出文件路径）设置到上面的Bin目录下。然后在需要使用导出的dll和lib文件的项目中设置项目依赖项这前者，并设置附加库目录和附加依赖项。这样可以很方便的使用同一解决方案中其他项目导出的链接库了。

```yml
通用属性

配置属性
    常规
        输出目录（.exe的目录）
        中间目录（中间文件的目录）
        MFC的使用 # [在共享DLL中使用MFC]
        字符集
    调试
        工作目录    # $(OutDir)
    C/C++
        常规
            附加包含目录    # 设置其他库的头文件的目录 $(ProjectDir)项目配置文件所在的目录；$(SolutionDir)解决方案配置文件所在的目录
            SDL检查
        代码生成
            启用最小重新生成 # 不用每次都编译整个项目
        语言
            符合模式 # 设置成否，就不会报const char*等错误
        预编译头
            预编译头 # 不使用
        命令行
            其他选项 # /utf-8 设置源文件的编码格式和执行文件的编码格式/source-charset:utf-8或者/execution-charset:utf-8 VS
                    # /
    链接器
        常规
            附加库目录      # 设置其他库的.lib以及.dll链接库的目录，程序运行需要将其拷贝到exe的当前目录或者将其放进PATH中
        输入
            附加依赖项      # 设置的就是xx.lib静态链接库的名称，qt项目（debug输入xxd.lib,release输入xx.lib）
    生成事件
        后期生成事件    # 重新生成程序之后执行的命令
        命令行          # 具体的命令，可以在这里使用copy命令，来将程序需要的.dll文件自动拷贝到.exe文件所在的目录。当然，在这里你可以做更多的事情，比如如果你的程序需要读取配置文件，你也可以把配置文件拷贝过来

```

### 3.3. utf-8

1. 参考[VS2017修改编码格式为utf-8，再也不用担心乱码了](VS2017修改编码格式为utf-8，再也不用担心乱码了)
2. 使用`ForceUTF8（No BOM）`插件。

## 4. 问题答疑

1. [could not find or load the Qt platform plugin windows的解决方法](https://blog.csdn.net/u012043391/article/details/71107724).
2. [VS工程 转 Qt 工程时 Qt VS Tools 功能键是灰色的问题](https://blog.csdn.net/ermzdy2/article/details/99692954)
3. [解决错误：error MSB8031: Building an MFC project for a non-Unicode character set is deprecated.](https://blog.csdn.net/weixin_40948750/article/details/85001480)
4. [QSqlDatabase::drivers() 输出为空解决方案](https://blog.csdn.net/weixin_37900004/article/details/112359253)
5. VS+Qt链接出现错误`LNK2001`，因为没有生成moc文件，需要按照其他h文件修改自己头文件的属性【其他执行方法】。
6. [【MFC】fatal error C1189: #error :  Building MFC application with /MD[d] (CRT dll version) requires MF](https://blog.csdn.net/qq_15698613/article/details/97285812).
7. [error C2440: “static_cast”](https://blog.csdn.net/myruo/article/details/82145960)
8. [error C2491](https://blog.csdn.net/gaofeidongdong/article/details/7781345)
9. [error C2664](https://blog.csdn.net/lljss1980/article/details/108547332)
10. [error C3867](https://blog.csdn.net/guqiwei/article/details/4535445)
11. [error C4996: 'strcpy': This function or variable may be unsafe. Consider using strcpy_s instead.](https://blog.csdn.net/qq_38721302/article/details/82850292)
12. [error C4996 ‘stricmp‘](https://blog.csdn.net/Baker_221b/article/details/107197285)
13. [error LNK2019: 无法解析的外部符号 _Netbios@4](https://www.jianshu.com/p/d66356fd2eaa)
14. [Windows 下 由SQLite3.def文件生成SQLite3.LIB文件](https://blog.csdn.net/qinbaby/article/details/83107345)
15. [vs2017无法打开“stdio.h”等源文件](https://blog.csdn.net/jfwzy109127/article/details/84190137)
16. [VS2017出现无法使用stdlib.h，string.h等SDK中的头文件](https://blog.csdn.net/qq_40737025/article/details/106984714)选择windows sdk然后重定向项目。

## 5. C++

1. `MFC`不能跨平台。
2. 使用自己的MSVC编译器，与其他编译器不兼容，甚至不同版本兼容性也不是很好。
3. 编码问题：MSVC使用GBK编码。

```markdown
1. 对于 MBCS 编码，字符变量用 char 定义。
2. 对于 Unicode 编码中，字符变量用 wchar_t 定义。
3. 在程序中使用 #include <tchar.h> 添加对 TCHAR 的支持。（我在vs里没加也编译通过了，不过还是加上的保险）
4. 对于字符串，例如 "abc" 用 _T("abc") 表示。就是加上 _T("")。
5. 定义字符变量时，将 char 换成 TCHAR。
6. 【尽量不要在源码中使用中文】。
```

### 5.1. Microsoft 专用的修饰符

```C++
#define DllExport   __declspec( dllexport ) //定义了可导出类，导出其所有成员函数和静态数据
#define DllImport   __declspec( dllimport ) //定义了可导入类，导入其所有成员函数和静态数据，一个程序中不能既是导出又是导入
```

