# 注释规范Doxygen


## 1. 安装

1. `apt-get install graphviz`;
2. `apt-get install doxygen`

## 2. 注释格式
  
### 2.1. 块注释，建议统一使用

```c++
/**
 *……
 */
```

### 2.2. 行注释

1. 如下图，函数中注释可以出现在Doxygen 页面的函数说明中。

```c++  
///< …
```

`/** …… */`

### 2.3. 常用注释

```c++
/**
@exception <exception-object> {exception description} 对一个异常对象进行注释。
@warning {warning message } 一些需要注意的事情
@todo { things to be done } 对将要做的事情进行注释，链接到所有TODO 汇总的TODO 列表
@bug 缺陷，链接到所有缺陷汇总的缺陷列表
@see {comment with reference to other items } 一段包含其他部分引用的注释，中间包含对其他代码项的名称，自动产生对其的引用链接。
@relates <name> 通常用做把非成员函数的注释文档包含在类的说明文档中。
@since {text} 通常用来说明从什么版本、时间写此部分代码。
@deprecated
@pre { description of the precondition } 用来说明代码项的前提条件。
@post { description of the postcondition } 用来说明代码项之后的使用条件。
@addtogroup 添加到一个组。
@deprecated 已废弃函数
@fn  函数说明
@ingroup 加入到一个组
@return  描述返回意义
@retval  描述返回值意义
@include 包含文件
@var、@enum、@struct、@class 对变量、美剧、结构体、类等进行标注
        + @attention
            + 【注意】
        + @brief
            +  概要信息
        + @code
            +  在注释中开始说明一段代码，直到@endcode命令。
        + @details
            + 详细描述
        + @note
            +  开始一个段落，用来描述一些注意事项
        + @par
            +  开始一个段落，段落名称描述由你自己指定
        + @param
            +  标记一个参数的意义
    + 常用注释
        + 项目注释
            + 放在核心文件中，比如main.c中
            + /** @mainpage 项目名称
*/
                + 可以包含@section 项目名称
                    + 项目描述
                        + ProjectName
                        + Author
                    + 功能描述
                    + 用法描述
                    + 版本描述
                    + 注意事项
                + 可以使用HTML的表格语法
            + SubTopic
        + 源代码文件注释
            + /**@file 【需与当前文件名一致】
* @brief       项目主函数文件
* @details  主要包含协议应用栈程序框架，main函数入口
* @author      wanghuan  any question please send mail to 371463817@qq.com
* @date        2018-8-17
* @version     V1.0
* @copyright    Copyright (c) 2018-2020  江苏亨通光网科技有限公司
* @attention
* 硬件平台:nRF52832_QFAA \n
* SDK版本：nRF5_SDK_15.0.0
* @par 修改日志:
*/
        + 函数注释
            + /**@brief NB模组向云平台上报数据
* @param[in]  handle              NB模组驱动句柄
* @param[in]  *data                上报数据指针
* @param[in]  len                上报数据长度
* @param[in]  rcc_enabled          上报时是否主动释放RCC链接
* @param[in]  update_enabled    上报时是否更新注册(只适用于onenet)
* @param[in]  report_fail_try_type    上报失败重新注册类型 \n
* @ref NB_REPFAIL_REG_TRY 出错立即重试    \n
* @ref NB_REPFAIL_REG_DELAY_TRY 出错延缓重试，在延迟期间如果正常则重新延缓，适用于高频率上报（上报失败重新注册超时15min） \n
* @ref NB_REPFAIL_REG_NO_TRY 出错不重试
* @return  函数执行结果
* - NB_NOTIFY_SUCCESS      上报成功
* - NB_NOTIFY_FAIL        上报失败
* - NB_IOT_REGIST_FAILED 注册失败返回
* - Others  其他错误
* @par 示例:
* @code
*    移动平台发送数据 AT+MIPLNOTIFY=0,122553,3308,0,5900,4,4,50,0,0
*    电信平台发送数据 AT+M2MCLISEND=000101
* @endcode
* @see :: ME3616_FxnTable
*/
        + 枚举、结构体等注释
        + 模块注释
        + 分组注释
## 配置VS Code

1. `Doxygen Documentation Generator`插件
2. 配置插件`setting.json`

```json
// The prefix that is used for each comment line except for first and last.
"doxdocgen.c.commentPrefix": " * ",
// Smart text snippet for factory methods/functions.
"doxdocgen.c.factoryMethodText": "Create a {name} object",
// The first line of the comment that gets generated. If empty it won't get generated at all.
"doxdocgen.c.firstLine": "/**",
// Smart text snippet for getters.
"doxdocgen.c.getterText": "Get the {name} object",
// The last line of the comment that gets generated. If empty it won't get generated at all.
"doxdocgen.c.lastLine": " */",
// Smart text snippet for setters.
"doxdocgen.c.setterText": "Set the {name} object",
// Doxygen comment trigger. This character sequence triggers generation of Doxygen comments.
"doxdocgen.c.triggerSequence": "/**",
// Smart text snippet for constructors.
"doxdocgen.cpp.ctorText": "Construct a new {name} object",
// Smart text snippet for destructors.
"doxdocgen.cpp.dtorText": "Destroy the {name} object",
// The template of the template parameter Doxygen line(s) that are generated. If empty it won't get generated atall.
"doxdocgen.cpp.tparamTemplate": "@tparam {param} ",
// File copyright documentation tag.  Array of strings will be converted to one line per element.  Can template{year}.
"doxdocgen.file.copyrightTag": [
  "@copyright Copyright (c) {year}"
],
// Additional file documentation.  Array of strings will be converted to one line per element.  Can template{year}, {date}, {author}, and {email}.
"doxdocgen.file.customTag": [],
// The order to use for the file comment. Values can be used multiple times. Valid values are shown in defaultsetting.
"doxdocgen.file.fileOrder": [
  "file",
  "author",
  "brief",
  "version",
  "date",
  "empty",
  "copyright",
  "empty",
  "custom"
],
// The template for the file parameter in Doxygen.
"doxdocgen.file.fileTemplate": "@file {name}",
// Version number for the file.
"doxdocgen.file.versionTag": "@version 0.1",
"doxdocgen.generic.authorEmail": "you@domain.com", //设置作者的邮箱
"doxdocgen.generic.authorName": "your name", //设置作者的姓名
// Set the style of the author tag and your name.  Can template {author} and {email}.
"doxdocgen.generic.authorTag": "@author {author} ({email})",
// If this is enabled a bool return value will be split into true and false return param.
"doxdocgen.generic.boolReturnsTrueFalse": true,
// The template of the brief Doxygen line that is generated. If empty it won't get generated at all.
"doxdocgen.generic.briefTemplate": "@brief {text}",
// The format to use for the date.
"doxdocgen.generic.dateFormat": "YYYY-MM-DD",
// The template for the date parameter in Doxygen.
"doxdocgen.generic.dateTemplate": "@date {date}",
// Decide if you want to get smart text for certain commands.
"doxdocgen.generic.generateSmartText": true,
// Whether include type information at return.
"doxdocgen.generic.includeTypeAtReturn": true,
// How many lines the plugin should look for to find the end of the declaration. Please be aware that settingthis value too low could improve the speed of comment generation by a very slim margin but the plugin also maynot correctly detect all declarations or definitions anymore.
"doxdocgen.generic.linesToGet": 20,
// The order to use for the comment generation. Values can be used multiple times. Valid values are shown indefault setting.
"doxdocgen.generic.order": [
  "brief",
  "empty",
  "tparam",
  "param",
  "return",
  "custom",
  "note"
],
// Custom tags to be added to the generic order. One tag per line will be added. You have to specify the prefixyourself.
"doxdocgen.generic.customTags": [],
// The template of the param Doxygen line(s) that are generated. If empty it won't get generated at all.
"doxdocgen.generic.paramTemplate": "@param {param} ",
// The template of the return Doxygen line that is generated. If empty it won't get generated at all.
"doxdocgen.generic.returnTemplate": "@return {type} ",
// Decide if the values put into {name} should be split according to their casing.
"doxdocgen.generic.splitCasingSmartText": true,
// Array of keywords that should be removed from the input prior to parsing.
"doxdocgen.generic.filteredKeywords": [],
// Substitute {author} with git config --get user.name.
"doxdocgen.generic.useGitUserName": false,
// Substitute {email} with git config --get user.email.
"doxdocgen.generic.useGitUserEmail": false
```

## 3. 配置文件

1. doxygen -s -g 这种方式生成的配置文件非常精简，没有任何注释。
2. 有了doxygen模板配置文件后，现在要做的就是对它进行修改了，下面以c语言开发的项目生成html为例.

```shell
# 项目名称，将作为于所生成的程序文档首页标题，需要使用双引号括住
PROJECT_NAME = “xxx”

# 文档版本号，可对应于项目版本号，譬如 git、svn、cvs 所生成的项目版本号
PROJECT_NUMBER = “1.0.0”

# 程序文档输出目录，产生的文件会放在这个路径之下。如果没有填这个路径，将会以当前所在路径来作为输出路径。
OUTPUT_DIRECTORY = doc/

# 程序文档语言环境，预设为 English。1.2.16 版后，可以用 Chinese 输出简体中文，也可以使用 
# Chinese-Traditional 来输出中文繁体的格式。
OUTPUT_LANGUAGE = Chinese

# 如果是制作 C 程序文档，该选项必须设为YES，否则默认生成 C++ 文档格式
OPTIMIZE_OUTPUT_FOR_C = YES

# 对于使用 typedef 定义的结构体、枚举、联合等数据类型，只按照 typedef 定义的类型名进行文档化
TYPEDEF_HIDES_STRUCT = YES

# 把这个标记设置为Yes，即使各个类或函数没有文档，也要提取信息
EXTRACT_ALL = YES

# 把这个标记设置为Yes，文档就会包含类的私有数据成员
EXTRACT_PRIVATE = YES

# 把这个标记设置为Yes，文档就会包含文件的静态成员(函数和变量)
EXTRACT_STATIC = YES

# 在 C++ 程序文档中,该值可以设置为 NO，而在 C 程序文档中，由于 C 语言没有所谓的域/名字空间
# 这样的概念，所以此处设置为 YES
HIDE_SCOPE_NAMES = YES

# 让 doxygen 静悄悄地为你生成文档，只有出现警告或错误时，才在终端输出提示信息
QUIET = YES

# 这个标记创建一个以空格分隔的所有目录的列表，
# 这个列表包含需要生成文档的 C/C++ 源代码文件和头文件，可以不设定，即为当前目录!
INPUT = inc/

# 输入文件的编码格式，需要自己根据INPUT指定的文件选择，否则会乱码
INPUT_ENCODING = UTF-8

# 在默认情况下,doxygen 会搜索具有典型 C/C++ 扩展名的文件，
# 比如 .c、.cc、.cpp、.h 和 .hpp 。
# 可以使用如下形式的列表方式,指定INPUT下要处理的文件
# 注:文件类型后有空格然后是右斜杠然后回车,每一个文件类均独自一行!
# FILE_PATTERNS = *.h \
                  *.c \
                  *.cpp
FILE_PATTERNS =

# 递归遍历由 INPUT 所指定的目录及其所有子目录，寻找由 FILE_PATTERNS 指定的要被文档化的文件
RECURSIVE = YES

# 如果您有特定文件或目录，不希望经过 Doxygen 处理，那你可在这指出，没有就不指出
# 例如:EXCLUDE = /home/xxx/src_root /home/xxx/test
EXCLUDE =

# 类似于 FILE_PATTERNS 的用法，只是这个是供 EXCLUDE 所使用
EXCLUDE_PATTERNS =

# 若设定为 YES，就会产生 HTML 版本的说明文件。HTML 文件是 Doxygen 预设产生的格式之一
GENERATE_HTML = YES

# 若设定为 YES，Doxygen 会帮您产生一个树状结构，在页面左侧，默认为 NO
# 这个树状结构是以 JavaScript 所写成。所以需要新版的 Browser 才能正确显示。
GENERATE_TREEVIEW = YES

```

## 4. 执行

`doxygen Doxyfile`生成文档了.注意：这里的Doxyfile为你实际的Doxygen配置文件名。

