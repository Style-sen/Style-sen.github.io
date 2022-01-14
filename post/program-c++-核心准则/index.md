# PROGRAM C++ 核心准则


## C.40 Define a constructor if a class has an invariant

## R.3: A raw pointer (a T*) is non-owning

## R.4: A raw reference (a T&) is non-owning

## R.10: Avoid malloc() and free() 避免使用malloc和free

1. malloc() and free() do not support construction and destruction, and do not mix well with `new` and `delete`.

## E

### E.28: Avoid error handling based on global state (e.g. errno)

1. 错误处理不要基于全局变量。（比较难于管理）

## I: Interfaces（接口）

1. 拥有良好的界面（易于理解，鼓励高效使用，不易出错，支持测试等）可能是代码组织的最重要方面。

### I.2: Avoid non-const global variables

1. 非常数全局变量，会隐藏依赖关系，而且会导致欲知的更改。
2. 【警惕：动态链接库的陷阱】[C++又一坑:动态链接库中的全局变量](https://blog.csdn.net/imxiangzi/article/details/45872025)。
3. 除非全局变量比单例更好。
