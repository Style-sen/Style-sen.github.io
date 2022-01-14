# GCC


## GCC

1. 查看编译器的默认搜索路径`echo | gcc -v -x c -E -`和`echo | g++ -v -x c++ -E -`;
2. 查看内部预宏定义`gcc -E -dM - </dev/null`.
3. 【注】linux下gcc不支标准c的gets,puts函数，可以用gcc fgets,fputs分别代替gets,puts。

### 编译

#### 编译选项

```shell
-fpermissive #允许编译一些不合格的代码。
```

### 交叉编译

#### Linaro
            + gcc-linaro-
                + 4.9.4-2017.01-
                    + glibc 2.17
                + 5.2-2015.11-
                + 5.1-2015.08-
                + 4.8-2014.04-
                + 4.7-2013.04-
