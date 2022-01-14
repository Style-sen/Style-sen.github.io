# LINUX调试和性能分析


## 1. 提升程序性能

### 1.1. 系统级性能优化

1. 两个阶段

#### 1.1.1. 性能剖析

1. 找出性能瓶颈
2. 查找原因和热点代码

#### 1.1.2. 代码优化

1. 【优化代码】和【编译选项】。

## 2. 性能剖析工具

### 2.1. [gperftools](https://github.com/gperftools/gperftools) *****

1. 应用级别。
2. gperftool主要支持以下四个功能：

    a. thread-caching malloc
    b. heap-checking using tcmalloc 堆检查器
    c. heap-profiling using tcmalloc 堆分析器
    d. CPU profiler CPU分析器

3. 如果要获得完整功能，链接应用代码的时候，需要链接动态库`-lprofiler -ltcmalloc`.

#### 安装

1. 参考[内存泄漏分析工具：tcmalloc](http://qiushao.net/2020/07/11/Linux/memory-leak-analyze-tcmalloc/index.html).
2. 克隆源码,提取出`tag`。
3. 没有libtool的话，需要安装`sudo apt-get install libtool`.
4. `./autogen.sh`.

##### PC

1. `mkdir x64_build`;
2. `./configure --prefix=$(pwd)/x64_build`;
3. `make`;
4. `make install`.

##### 交叉编译生成so动态库

1. `source`使用交叉编译器。
2. `./configure --prefix=$(pwd)/arm_build --host=arm-linux`.
3. `make clean`.
4. `make -j8`.
5. `make install`.

#### 使用

1. 应用程序生成分析源文件(需要设置一些环境变量)，然后在PC上分析(`pprof`perl脚本)，此时需要[修改应用程序源码并链接相应的库](https://blog.51cto.com/wulingdong/2043898)。

### 2.2. Oprofile

1. 更新停滞。

### 2.3. Perf *****

1. 内核级别(一般用来分析CPU，分析内存可用gperftools)【必须在内核中打开perf的events功能】。
2. 开源。
3. 内置于内核源码树。
4. 基于事件采样原理:每隔一个固定的时间，产生一个中断，然后统计对应的pid和函数【采样就预示着与实际运行情况并不能保持一致，但如果一个函数运行的时间越长，被时钟中断的机会就越大。鉴于perf最终显示的是统计值，所以它的测量结果是高度可信的】。
5. perf支持两种模式，计算模式和采样模式。比如，perf stat使用的是计算模式，而perf record采用的是采样模式。

#### 2.3.1. 以性能事件为基础

1. 支持针对处理器相关性能指标与操作系统相关性能指标的性能剖析.
1. 主要有3类。

##### 2.3.1.1. Hardware Event 是由 PMU 硬件产生的事件

###### 2.3.1.1.1. cache 命中

##### 2.3.1.2. Software Event 是内核软件产生的事件

###### 2.3.1.2.1. 进程切换

###### 2.3.1.2.2. tick 数

##### 2.3.1.3. Tracepoint event 是内核中的静态 tracepoint 所触发的事件，这些 tracepoint 用来判断程序运行期间内核的行为细节

###### 2.3.1.3.1. slab 分配器的分配次数

#### 2.3.2. 安装

1. 源码存在`linux`内核源码`tools/perf`中。

##### 2.3.2.1. 交叉编译

1. 需使用和编译内核一致的编译器。
2. 需要查看是否需要安装相应的插件.

    ```shell
    Auto-detecting system features:
    ...                         dwarf: [ OFF ]
    ...                         glibc: [ on  ]
    ...                          gtk2: [ OFF ]
    ...                      libaudit: [ OFF ]
    ...                        libbfd: [ OFF ]
    ...                        libelf: [ OFF ] # 需要elf
    ...                       libnuma: [ OFF ]
    ...                       libperl: [ OFF ]
    ...                     libpython: [ OFF ]
    ...                      libslang: [ OFF ]
    ...                     libunwind: [ OFF ]
    ...            libdw-dwarf-unwind: [ OFF ]
    ...                          zlib: [ OFF ]
    ...                          lzma: [ OFF ]
    ```

3. 安装`zlib`1.2.11.

    ```shell
    1. 下载源码；
    2. 更新环境变量，使用交叉编译器。
    3. `mkdir arm_build`;
    4. `./configure --prefix=$(pwd)/arm_build`;
    5. `make`
    6. `make install`.
    ```

4. 安装`elfutils`0.183.(work)

    ```shell
    1. 下载"https://sourceware.org/elfutils/ftp/".
    2. `mkdir arm_build`;
    3. `./configure --prefix=$(pwd)/arm_build --host=arm-linux LDFLAGS="-L[zlib_path] -lz" CPPFLAGS=-I[zlib_include] --disable-libdebuginfod --disable-debuginfod`;
    4.  `make`
    5.  `make install`.
    ```

5. 交叉编译`perf`:

    ```shell
    1. 修改Makefile.config`EXTLIBS`增加-lelf -lz
    1. make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
    ```

6. 遗留问题，静态编译(LDFLAGS+=-static)失败，只能动态编译???。

7. symbol不显示函数名`sudo apt-get install libiberty-dev binutils-dev`.

#### 2.3.3. 使用

1. `perf list`列出所有能够触发`perf`采样点的事件.

##### 2.3.3.1. `perf stat [程序]`

1. 最先使用的一个工具;
2. 通过概括精简的方式提供被调试程序运行的整体情况和汇总数据.

###### 2.3.3.1.1. 指标分析

1. `task-clock`CPU 利用率，该值高，说明程序的多数时间花费在 CPU 计算上而非 IO.
2. `context-switches`进程切换次数，记录了程序运行过程中发生了多少次进程切换，频繁的进程切换是应该避免的。
3. `cpu-migrations`表示进程 t1 运行过程中发生了多少次 CPU 迁移，即被调度器从一个 CPU 转移到另外一个 CPU 上运行
4. `page-faults`;
5. `cycles`处理器时钟，一条机器指令可能需要多个 cycles;
6. `instructions`机器指令数目;
7. `branches`
8. `branch-misses`
9. 使用 -e 选项来查看您所感兴趣的特殊的事件.

##### 2.3.3.2. `perf top`

1. 实时显示当前系统的性能统计信息。该命令主要用来观察整个系统当前的状态.
2. 查看当前系统最耗时的[内核函数]或[某个用户进程].
3. -e 选项，您可以列出造成其他事件的 TopN 个进程 / 函数

##### 2.3.3.3. `perf record`

1. 记录单个函数级别的统计信息,使用`-o`记录到文件中，并使用 perf report 来显示统计结果

### 2.4. Strace

1. strace是一个可用于诊断、调试和教学的Linux用户空间跟踪器。我们用它来监控用户空间进程和内核的交互，比如【`系统调用`】、信号传递、进程状态变更等.
2. strace底层使用内核的ptrace特性来实现其功能.
3. 基本使用`strace -tt -f  ./some_server ../conf/some_server.conf`.

#### 2.4.1. 交叉编译

1. clone[strace](https://github.com/strace/strace.git);
2. 使用`gcc-linaro-7.5.0-2019.12-x86_64_arm-linux-gnueabihf`;
3. `./bootstrap`;
4. `./configure --prefix=$(pwd)/arm_build --host=arm-none-linux`;
5. `make`;
6. `make install`;

#### 2.4.2. 使用

### 2.5. Top

#### 2.5.1. 内存

```shell
Mem: 172520K used, 858088K free, 0K shrd, 1053056K buff, 1053104K cached
```

#### 2.5.2. CPU

```shell

```

1. `sys 占用高需要查看系统调用`。

#### 2.5.3. 各列

1. VSZ 虚拟内存。
2. STAT 进程状态。

### 2.6. VTune

1. Intel平台。
2. 收费。

### 2.7. shell脚本

1. [f18m/CPU-MEM-monitor](https://github.com/f18m/CPU-MEM-monitor)复杂脚本.
2. 简单脚本。

```shell
#!/bin/sh
interval=1
if [ "$1" != "" ]; then
    interval=$1
else
    exit
fi
if [ "$2" != "" ]; then
    procname=$2
else
    exit
fi
echo "检查时间间隔(单位秒)："$interval
echo "进程名称:"$procname
datetime=$(date +'%Y%m%d')
while :; do
    #根据进程名获取进程id
    pid=$(ps -ef | grep $procname | grep -v grep | awk '{print $1}')
    echo $pid $procname
    #获取当读占用cpu信息
    echo $(date +'%Y-%m-%d %H:%M:%S') %cpu: $(top -n 1 -d 1 | grep $procname | grep -v grep | awk '{print $8}') >>/program/${procname}_cpu_stat.txt
    sleep $interval
done

```

## 3. 程序性能问题的原因

1. 程序的运算量很大，导致 CPU过于繁忙，CPU是瓶颈.
2. 程序需要做大量的 I/O，读写文件、内存操作等等，CPU 更多的是处于等待，I/O部分称为程序性能的瓶颈.
3. 程序之间相互等待，结果 CPU 利用率很低，但运行速度依然很慢，事务间的共享与死锁制约了程序的性能.

