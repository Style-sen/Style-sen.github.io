# Docker笔记


## Docker进程管理

参考[从一个实例，一窥docker进程管理](https://zhuanlan.zhihu.com/p/56350784)

1. 在Docker中，进程管理的基础是Linux内核的PID命名空间技术。在不同的PID命名空间下，可以有相同的PID。
2. Linux内核为所有的PID命名空间维护了一个树状的数据结构，最顶层是系统初始化时创建的root namespace（根命名空间）， 父节点可以看到子节点中的进程，并可以通过信号等方式对子节点中的进程产生影响。反过来，子节点不能看到父节点名空间中的任何内容，也不可能通过kill或ptrace影响父节点或其他名空间中的进程。
3. 在`docker中有一个很特殊的进程——PID为1的进程，这也是docker的主进程(支持这一个)`，通过Dockerfile中的 ENTRYPOINT 和/或 CMD指令指定。当主进程退出的时候，容器所拥有的PIG命名空间就会被销毁，容器的生命周期也会结束docker最佳实践建议的是`一个container一个service`，并不强制要你一个container一个线程。有的服务，会催生更多的子进程，比如Apache和uwsgi，这是完全OK的。

## 安装

1. [使用清华源](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)
```
sudo snap install docker
```
2. The Docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root and other users can only access it using sudo. The Docker daemon always runs as the root user.
3. 将用户添加到docker组中，`sudo usermod -aG docker jayzhen`只有不用重启docker，并重新登录。

## 配置



## Dockerfile

### ADD

1. ADD指令可以让你使用URL作为<src>参数。当遇到URL时候，可以通过URL下载文件并且复制到<dest>。
2. ADD的另外一个特性是有能力自动解压文件。如果<src>参数是一个可识别的压缩格式（tar, gzip, bzip2, etc）的本地文件（所以实现不了同时下载并解压），就会被解压到指定容器文件系统的路径<dest>。

### COPY



### ENTRYPOINT  CMD 指定启动容器执行的命令

1. 在指令格式上，一般推荐使用 exec 格式（`不启动shell`），这类格式在解析时会被解析为 JSON 数组，因此一定要使用双引号 "，而不要使用单引号。
```
CMD ["executable","param1","param2"] 使用 exec 执行，推荐方式；
CMD ["sh", "shell脚本"]
```

2. 如果使用 shell 格式的话，实际的命令会被包装为 sh -c 的参数的形式进行执行。
```
CMD command param1 param2    如： CMD echo $HOME
CMD ["param1","param2"] 提供给 ENTRYPOINT 的默认参数；
```
2. 每个 Dockerfile 只能有一条 CMD 命令。如果指定了多条命令，只有最后一条会被执行。
3. 如果用户启动容器时候指定了运行的命令，则会覆盖掉 CMD 指定的命令。
5. Docker 不是虚拟机，容器中的应用都应该以前台执行，而不是像虚拟机、物理机里面那样，用 upstart/systemd 去启动后台服务，容器内没有后台服务的概念。
6. 对于容器而言，其启动程序就是容器应用进程，容器就是为了主进程而存在的，主进程退出，容器就失去了存在的意义，从而退出，其它辅助进程不是它需要关心的东西。
7. 至少有一个CMD或者ENTRYPOINT。
8. ENTRYPOINT should be defined when using the container as an executable.
9. CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.
10. CMD will be overridden when running the container with alternative arguments.
11. If CMD is defined from the base image, setting ENTRYPOINT will reset CMD to an empty value. In this scenario, CMD must be defined in the current image to have a value.

||	No ENTRYPOINT|	ENTRYPOINT exec_entry p1_entry|	ENTRYPOINT [“exec_entry”, “p1_entry”]|
|--|--|--|--|
|No CMD	                                                        |error, not allowed	|/bin/sh -c exec_entry p1_entry                        |exec_entry p1_entry |
|CMD [“exec_cmd”, “p1_cmd”]	    |exec_cmd p1_cmd	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry exec_cmd p1_cmd|
|CMD [“p1_cmd”, “p2_cmd”]	    |p1_cmd p2_cmd	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry p1_cmd p2_cmd|
|CMD exec_cmd p1_cmd	                    | /bin/sh -c exec_cmd p1_cmd	|/bin/sh -c exec_entry p1_entry	|exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd|

### 最佳实践

1. 使用`.dockerignore`排除构建无关文件，可提高构建速度。
```
当我们在 docker build 的过程中，首先会将指定的上下文目录`Sending build context to Docker daemon  27.41MB
`打包传递给 docker引擎，而这个上下文目录中可能并不是所有的文件我们都会在 Dockerfile 中使用到，那么这个时候就可以在 .dockerignore 文件中指定在传递给 docker引擎时需要忽略掉的文件或文件夹。
```
2. 使用多阶段构建。
3. `一个容器只做一件事`，需要网络通信，则可以将他们置于一个网络下。
4. `减少镜像层数`，只有`RUN`,`COPY`,`ADD`会创建层数，其他指令不会增加层数。
5. 将多行参数进行排序。
6. 充分利用构建缓存。



## 配置网络

### bridge

1. 默认。

### host

1. 容器与主机享受相同的network namespace。

### overlay

### macvlan

### none

### Network plugins

### 命令

1. `docker network create`创建一个网络。
2. `docker network ls`列出网络。

#### docker commit

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
OPTIONS说明：
    -a :提交的镜像作者；
    -c :使用Dockerfile指令来创建镜像；
    -m :提交时的说明文字；
    -p :在commit时，将容器暂停。
```

## 解决问题

1. [GitLab CI with docker and dind "mount: permission denied (are you root?)"](https://www.cnblogs.com/shuaiandjun/p/9484540.html)
2. Docker容器修改hosts文件重启不变
    1./etc/hosts, /etc/resolv.conf和/etc/hostname，容器中的这三个文件不存在于镜像，而是存在于/var/lib/docker/containers/<container_id>，在启动容器的时候，通过mount的形式将这些文件挂载到容器内部。因此，如果在容器中修改这些文件的话，修改部分不会存在于容器的top layer，而是直接写入这三个物理文件中。
    2.为什么重启后修改内容不存在了？原因是：每次Docker在启动容器的时候，通过重新构建新的/etc/hosts文件，这又是为什么呢？原因是：容器重启，IP地址为改变，hosts文件中原来的IP地址无效，因此理应修改hosts文件，否则会产生脏数据。
3. [docker运行nginx为什么要使用 daemon off](https://segmentfault.com/a/1190000009583997)
4. 宿主机可以访问外网，但是容器不能，`重启Docker可以解决（或者升级Docker并重启宿主机）`。
5. docker中使用定时任务，定时任务放进`supervisord`中.

## 选择镜像

### Ubuntu

### CENTOS
1. 使用Docker创建容器时，基础镜像通常选择Ubuntu或Centos，不管哪个镜像的大小都在100MB以上。

### ALPINE
2. `Alpine Linux的最大优势是小`，Alpine Linux是一个面向安全的轻型的Linux发行版，Alpine Linux采用了 musl libc 和 busybox以减小系统的体积和运行时资源消耗。在保持瘦身的同时，Alpine Linux还提供了自己的包管理工具apk。
3. Alpine Linux使用了musl，可能和其他Linux发行版使用的glibc实现会有些不同。
4. musl实现的DNS服务不会使用resolv.conf文件中的search和domain两个配置，通过DNS来进行服务发现时需要注意。
5. [sgerrand / alpine-pkg-glibc](https://github.com/sgerrand/alpine-pkg-glibc).
6. [Alpine与glibc](https://www.szyhf.org/2016/12/16/alpine%E4%B8%8Eglibc/)。
7. 安装GCC `apk add build-base` 安装基础库（包含GCC、libc-dev、binutils）。
8. `OSError: mysql_config not found`执行`apk add mariadb-dev mariadb-client`.
9. `fatal error: linux/limits.h` 执行 `apk add linux-headers`.
10. `The headers or library files could not be found for jpeg` 执行 `apk add libjpeg-turbo-dev`.
11. `RUN pip install "celery[librabbitmq,redis,msgpack]"` 安装过程中出错，“poll or select”只需要安装6安装glibc即可。

### NGINX 官方镜像

#### nginx:alpine

1. 包含`envsubst`命令。

## **资源限制** *****

参考[Docker(二十)-Docker容器CPU、memory资源限制](https://www.cnblogs.com/zhuochong/p/9728383.html)

1. 在使用 docker 运行容器时，默认的情况下，docker没有对容器进行硬件资源的限制，当一台主机上运行几百个容器，这些容器虽然互相隔离，但是底层却使用着相同的 CPU、内存和磁盘资源。如果不对容器使用的资源进行限制，那么容器之间会互相影响，小的来说会导致容器资源使用不公平；大的来说，可能会导致主机和集群资源耗尽，服务完全不可用。
2. docker 作为容器的管理者，自然提供了控制容器资源的功能。正如使用内核的 namespace 来做容器之间的隔离，docker 也是通过内核的 cgroups 来做容器的资源限制；包括CPU、内存、磁盘三大方面，基本覆盖了常见的资源配额和使用量控制。
3. Docker内存控制OOME在linxu系统上，如果内核探测到当前宿主机已经没有可用内存使用，那么会抛出一个OOME(Out Of Memory Exception:内存异常 )，并且会开启killing去杀掉一些进程。
4. 一旦发生OOME，任何进程都有可能被杀死，包括docker daemon在内，为此，docker特地调整了docker daemon的OOM_Odj优先级，以免他被杀掉，但容器的优先级并未被调整。经过系统内部复制的计算后，每个系统进程都会有一个OOM_Score得分，OOM_Odj越高，得分越高，（在docker run的时候可以调整OOM_Odj）得分最高的优先被kill掉，当然，也可以指定一些特定的重要的容器禁止被OMM杀掉，在启动容器时使用 –oom-kill-disable=true指定。

### Cgroup

1. 可以使用stress工具来测试CPU和内存.

### 内存限制
1. Docker 提供的内存限制功能有以下几点：
a. 容器能使用的内存和交换分区大小。
b. 容器的核心内存大小。
c. 容器虚拟内存的交换行为。
d. 容器内存的软性限制。
e. 是否杀死占用过多内存的容器。
f. 容器被杀死的优先级.
2. 一般情况下，达到内存限制的容器过段时间后就会被系统杀死。
3. 执行docker run命令时能使用的和内存限制相关的所有选项如下。
|选项|	描述|
|--|--|
|-m,--memory	      |内存限制，格式是数字加单位，单位可以为 b,k,m,g。最小为 4M|
|--memory-swap	      |内存+交换分区大小总限制。格式同上。必须比-m设置的大|
|--memory-reservation |	内存的软性限制。格式同上|
|--oom-kill-disable	  |是否阻止 OOM killer 杀死容器，默认没设置|
|--oom-score-adj	  |容器被 OOM killer 杀死的优先级，范围是[-1000, 1000]，默认为 0|
|--memory-swappiness  |用于设置容器的虚拟内存控制行为。值为 0~100 之间的整数|
|--kernel-memory	  |核心内存限制。格式同上，最小为 4M |

### CPU限制

### 磁盘IO限制

## 高级应用

### [Source-To-Image (S2I)](https://github.com/openshift/source-to-image)

1. 参考[s2i简介](https://www.jianshu.com/p/4a44eb296ddb)

#### s2i原理

1. s2i是红帽推出的一种基于容器的应用镜像构建工具。它需要三个元素：源码，基础镜像，构建配置。大概流程如下：
(1)首先用户需要制作一个基础镜像，作为构建的基础环境，用户需要配置好相应的软件包，环境变量，等等;
(2)s2i负责启动一个容器（其镜像为用户指定的基础镜像）;
(3)s2i将源码拉取到容器中的/tmp/src目录下，然后读取s2i的配置信息进行源码编译及安装配置;
(4)s2i将编译好的容器提交成新的镜像.

