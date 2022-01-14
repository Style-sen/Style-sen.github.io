# C/C++ 标准库


## 1. <arpa/inet.h>

### 1.1. 宏

### 1.2. 结构体

### 1.3. 函数原型

```c++
unsigned long int inet_addr(const char *cp)//将一个字符串格式的ip地址转换成一个uint32_t数字格式，"127.0.0.1">0x100007F
```

## 2. <errno.h>

1. 定义了 errno 和各种常量。

## 3. <fcntl.h>

### 3.1. 宏

### 3.2. 函数原型

#### 3.2.1. `int fcntl (int __fd, int __cmd, ...);`

1. 针对(文件)描述符提供控制.

#### 3.2.2. `int open (const char *__file, int __oflag, ...)`

1.

## 4. <setjmp.h>

## 5. <signal.h>

## 6. <stdio.h> 标准 IO 函数库

### 6.1. 宏

1. EOF
2. stdin
3. stdout

### 6.2. 函数原型

1. `printf()`%p的实质即为将所指向的数据地址以十六进制的形式输出；对于指针来说，%p的打印的为所指向的数据地址。指针自己的实际地址为&p。负值为左对齐。
2. `getc()`从文件描述符获取一个字符
3. `putc()`向文件描述符写一个字符
4. `fgets()`从文件描述符读取一行

#### 6.2.1. `void perror (const char *__s);`

1. 把一个描述性错误消息输出到标准错误 stderr。首先输出字符串 str，后跟一个冒号，然后是一个空格。

#### 6.2.2. int atoi (const char * str)

1. 将字符串转换成整数。
2. atoi() 函数会扫描参数 str 字符串，跳过前面的空白字符（例如空格，tab缩进等，可以通过 isspace() 函数来检测），直到遇上数字或正负符号才开始做转换，而再遇到非数字或字符串结束时('\0')才结束转换，并将结果返回。
3. 返回转换后的整型数；如果 str 不能转换成 int 或者 str 为空字符串，那么将返回 0。

#### 6.2.3. double atof (const char* str)

1. 将字符串转换为双精度浮点数。

## 7. <stdlib.h>

1. 定义了四个变量类型、一些宏和各种通用工具函数.

### 函数原型

```c++
1. void *bsearch(const void *key, const void *base, size_t nitems, size_t size, int (*compar)(const void *, const void *)) 对 nitems 对象的数组执行二分查找，base 指向进行查找的数组，key 指向要查找的元素，size 指定数组中每个元素的大小。
```

## 8. <string.h>

1. `void *memset (void *__s, int __c, size_t __n)`复制字符 c（一个无符号字符）到参数 str 所指向的字符串的前 n 个字符.
2. `char *strchr(const char *str, int c)`在参数`str`所指向的字符串中搜索第一次出现字符`c`（一个无符号字符）的位置.未找到该字符则返回 NULL.
3. `strerror()`传入 errno，输出对应的错误字符串。
4. `int strncasecmp (const char *__s1, const char *__s2, size_t __n)`
5. `char *strstr(const char *haystack, const char *needle)`返回在`haystack`中第一次出现`needle`字符串的位置，如果未找到则返回 null;不包含终止符 '\0'.

## 9. <sysdefs.h>

## 10. <sys/stat.h>

### 10.1. 宏

## 11. <sys/wait.h>

## 12. <time.h>

### 12.1. 函数原型

#### 12.1.1. `time(time_t *t)`

1. 此函数会返回从公元 1970 年 1 月 1 日的 UTC 时间从 0 时 0 分 0 秒算起到现在所经过的秒数。如果 t 并非空指针的话，此函数也会将返回值存到 t 指针所指的内存。

## 13. <unistd.h>封装了类 UNIX 系统下的很多固定名称的 system_call 系统调用

### 13.1. 宏

1. STDIN_FILENO 标准输入的文件描述符
2. STDOUT_FILENO 标准输出的文件描述符

### 13.2. 函数原型

1. `getpid()` 获取当前进程的进程 ID
2. `getppid()` 获取当前进程的父进程 ID
3. \_exit()
4. `close()`

#### 13.2.1. `ssize_t read (int __fd, void *__buf, size_t __nbytes)`

1. 如果文件描述符不是 socket 的话，该函数是非阻塞的，否则该函数是阻塞的。

## 14. <wchar.h> 标准 IO

