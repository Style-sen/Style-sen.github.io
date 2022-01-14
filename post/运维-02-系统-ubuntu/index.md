# Ubuntu服务器系统及基础设施


## 1. U盘安装

1. windows 7以上系统；
2. 下载[Rufus](https://rufus.ie/);
3. 下载Ubuntu server镜像；
4. 使用Rufus制作启动U盘；
5. U盘启动即可安装。

## 2. 文件系统

| 根目录 | 一级子目录 | 二级子目录 | 描述                                                                                                 |
| ------ | ---------- | ---------- | ---------------------------------------------------------------------------------------------------- |
| /usr   |            |            | 这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录 |
|        | /include   |            | 用户安装的lib库的头文件都在这里                                                                      |

## 3. 系统管理

### 3.1. SHELL终端

#### 3.1.1. SH

#### 3.1.2. BASH

1. bash shell的命令分为两类：`外部命令`和`内部命令`。外部命令是通过系统调用或独立的程序实现的，如sed、awk等等。内部命令是由特殊的文件格式(.def)所实现，如cd、history、exec等等。
2. `fork`是linux的系统调用，用来创建子进程(child process)。子进程是父进程(parent process)的一个副本，从父进程那里获得一定的资源分配以及继承父进程的环境。子进程与父进程唯一不同的地方在于pid(process id)。
3. 环境变量只能单向从父进程传给子进程。不管子进程的环境变量如何变化，都不会影响父进程的环境变量。
4. `exec`命令不产生新的子进程。在执行时会把当前的shell process关闭，然后换到后面的命令继续执行。PID不变。

### 3.2. PATH

1. 普通用户使用$PATH；
2. 使用sudo去执行一个程序时，出于安全的考虑，这个程序将在新的、最小化的环境中执行。也就是说，诸如PATH这样的环境变量，在sudo命令下已经被重置成默认的状态了。所以当一个刚初始化的PATH变量中不会包含你所要运行的程序所在的目录，用sudo去执行，你就会得到“command not found”的错误。
要改变PATH在sudo会话中的初始值，打开/etc/sudoers文件，找到“secure_path”一行。当值执行sudo命令时，”secure_path“中包含的路径将被当做默认PATH变量使用。比如加入/usr/local/bin到其中：
`Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin:/usr/local/bin`
更改会即可生效。

### 3.3. systemctl

Systemctl是一个systemd(系统初始化程序，系统开始的第一个进程，pid为1)工具，主要负责控制systemd系统和服务管理器。

### 3.4. `iostat` 查看磁盘IO

1. avg-cpu：为总体CPU使用统计情况，对于多核CPU，这里为所有CPU的平均值;主要看iowait的值，它指示CPU用于等待io请求完成的时间。
2. Device：为各磁盘设备的io统计信息。
3. Device中各列含义:
a. Device：设备名称
b. tps：每秒进程下发的IO读、写请求数量
c. Blk_read/s：每秒读扇区数量（一扇区为512bytes）
d. Blk_wrtn/s：每秒写扇区数量
e. Blk_read：取样时间间隔内读扇区总数量
f. Blk_wrtn：取样时间间隔内写扇区总数量
4. `iostat 1 3`表示每隔1秒打印1次，共打印3次。iostat命令还有一个非常实用的选项-x，它可以显示更多的信息，也是一个最常用的选项.
5. -d选项可以把CPU相关信息过滤掉，只显示磁盘相关信息:
rrqm/s：每秒对该设备的读请求被合并次数，文件系统会对读取同块（block）的请求进行合并。
wrqm/s：每秒对该设备的写请求被合并次数。
r/s：每秒完成的读次数。
w/s：每秒完成的写次数。
rkB/s：每秒读数据量（kB为单位）。
wkB/s：每秒写数据量（kB为单位）。
avgrq-sz：平均每次IO操作的数据量（扇区数为单位）。
avgqu-sz：平均等待处理的IO请求队列长度。
await：平均每次IO请求等待时间（包括等待时间和处理时间，毫秒为单位）。
svctm：平均每次IO请求的处理时间（毫秒为单位）。
%util：采用周期内用于IO操作的时间比率，即IO队列非空的时间比率。
6. 最应该关注的是%util，磁盘使用率100%，其实就是%util的值为100%。

### 3.5. `iotop` 查看哪个进程磁盘读写最高

### 3.6. netstat

|     |             |
| --- | ----------- |
| -t  | --tcp       | TCP              |
| -l  | --listening | 显示监听的socket |

### 3.7. lsof

1. 列出打开文件，在linux中一切皆文件

### 3.8. nc

1. 验证网络连接.

### 3.9. 为网络连接设置文件描述符

### 3.10. 包管理

1. 优先snap包管理器。
2. 系统中最好安装有一个版本的docker。

### 3.11. STRACE

1. strace常用来跟踪进程执行时的系统调用和所接收的信号。 在Linux世界，进程不能直接访问硬件设备，当进程需要访问硬件设备(比如读取磁盘文件，接收网络数据等等)时，必须由用户态模式切换至内核态模式，通过系统调用访问硬件设备。strace可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间。

    ```shell
    root@ubuntu:/usr# strace cat /dev/null 
    execve("/bin/cat", ["cat", "/dev/null"], [/* 22 vars */]) = 0
    brk(0)                                  = 0xab1000
    access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
    mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f29379a7000
    access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
    ...
    brk(0) = 0xab1000
    brk(0xad2000) = 0xad2000
    fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(136, 0), ...}) = 0
    open("/dev/null", O_RDONLY) = 3
    fstat(3, {st_mode=S_IFCHR|0666, st_rdev=makedev(1, 3), ...}) = 0
    read(3, "", 32768) = 0
    close(3) = 0
    close(1) = 0
    close(2) = 0
    exit_group(0) = ?
    ```

2. 每一行都是一条系统调用，等号左边是系统调用的函数名及其参数，右边是该调用的返回值。
3. strace 显示这些调用的参数并返回符号形式的值。strace 从内核接收信息，而且不需要以任何特殊的方式来构建内核。

### 3.12. Command

|       |                                                                        |
| ----- | ---------------------------------------------------------------------- |
| xargs | [Linux xargs 命令](https://www.runoob.com/linux/linux-comm-xargs.html) |

### 3.13. shell脚本

1. 在脚本中使用export设置环境变量，`./`之后没有生效。原因是执行脚本用./test.sh的方式，如果采用source test.sh，则环境变量会生效。
2. `直接执行一个脚本文件是在一个子shell中运行的，而source则是在当前shell环境中运行的`。

```conf
#! /bin/zsh

function mimvp_randnum_uuid(){
    min=$1
    max=$2
    mid=$(($max-$min+1))
    num=$(head -n 20 /proc/sys/kernel/random/uuid | cksum | cut -f1 -d ' ')
    randnum=$(($num%$mid+$min))
    echo $randnum
}

function print_randnum_uuid(){
    for i in {1..50};
    do 
        randnum=$(mimvp_randnum_uuid 40000000 50000000)
        #echo -e "$i \t $randnum"
        echo $randnum
    done
}

print_randnum_uuid
```

### 3.14. 定时任务

参考[Ubuntu 16.04设置crontab定时计划任务](https://www.lulinux.com/archives/2258)
参考[每天一个linux命令（50）：crontab命令](https://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)

1. crond是linux下用来周期性的执行某种任务或等待处理某些事件的一个守护进程，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务工具（`如果使用的容器镜像，可能被裁减`），并且会自动启动crond进程，crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。
2. Linux下的任务调度分为两类，`系统任务调度`和`用户任务调度`。
3. 在`/etc/crontab`文件，是系统任务调度的配置文件。
4. 用户任务调度：用户定期要执行的工作。用户可以使用 crontab 工具来定制自己的计划任务。所有用户定义的crontab 文件都被保存在 `/var/spool/cron/crontabs`目录中。其文件名与用户名一致。

#### 3.14.1. crontab命令

### 3.15. 系统日志

#### 3.15.1. rsyslog 日志收集

## 4. SSH访问

### 4.1. 常用操作

1. 上传目录到服务器
`scp  -r /tmp/local_dir username@servername:remote_dir`

2. 免密登录

    ```shell
    #客户端配置
    ssh-keygen -t rsa  //（会有提示输入密码，直接回车即可）
    # 之后会在家目录下建立 .ssh 文件夹，内有id_rsa（私钥）和id_rsa.pub（公钥）两个文件
    # 将公钥文件上传到服务器上server的家目录
    scp ~/.ssh/id_rsa.pub server@192.168.0.10:~

    # 服务器端配置
    # 使用server用户登录并进入家目录
    # cd /home/server
    # 建立 .ssh 文件夹
    mkdir .ssh;
    # 修改文件夹权限【Ubuntu和Centos一样】
    chmod 700 .ssh
    # 将客户端传过来的id_rsa.pub文件中的内容加入到authorized_keys中
    cat id_rsa.pub >> .ssh/authorized_keys
    chmod 644 .ssh/authorized_keys 【Ubuntu和Centos不一样】
    #之后你从客户端使用 client 用户登录服务器的server用户时就不需要再输入密码了，但是第一次连接时还是会提示你进行远程主机公钥指纹核对并将其存入 ~/.ssh/known_hosts 文件。
    ```

3. 免密登录之后，还`需要输入密码`：

    ```shell
    【Centos】
    首先我们就要去查看系统的日志文件
    tail /var/log/secure -n 20 【tail /var/log/auth.log】
    发现问题的所在：Authentication refused: bad ownership or modes for file
    从字面上可以看出是目录的属主和权限配置不当，查找资料得知：SSH不希望home目录和~/.ssh目录对组有写权限，通过下面几条命令改下
    chmod g-w /home/zhangming
    chmod 700 /home/zhangming/.ssh
    chmod 600 /home/zhangming/.ssh/authorized_keys
    ```

4. 多个私钥

    ```shell
    临时生效
    ssh-agent bash
    # ssh-add 私钥名称
    ssh-add id_rsa

    修改配置文件
    cd  ~/.ssh
    touch  config
    chmod  600  ~/.ssh/config

    #主机服务器  , 
    Host         别名
    HostName     填入云服务器ip
    Port         22
    User    用户名
    IdentityFile ~/.ssh/私钥文件名
    ```

### 4.2. 修改SSH默认端口（安全第一步）

1. 备份原sshd配置文件`cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup`
2. 修改sshd_config文件，打开`port 22`,再新增一个Port，保存。
3. 重启sshd服务，`systemctl restart sshd`或`/etc/init.d/sshd restart`或`/etc/init.d/ssh restart`。ss -ntl 或 netstat -ntl 查看一下端口。
4. 防火墙（iptables）、安全组（ECS）规则设置。

    ```shell
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 60022 -j ACCEPT
    $ systemctl restart iptables.service
    $ systemctl restart sshd.service
    ```

### 4.3. 增加系统管理员（非超级管理员）用户（安全第二步）

1. 添加新用户`sudo useradd mangues`

    ```shell
    useradd与adduser都是创建新的用户
    1、useradd在使用该命令创建用户是不会在/home下自动创建与用户名同名的用户目录，而且不会自动选择shell版本，也没有设置密码，那么这个用户是不能登录的，需要使用passwd命令修改密码。
    2、adduser在使用该命令创建用户是会在/home下自动创建与用户名同名的用户目录，系统shell版本，会在创建时会提示输入密码，更加友好。
    ————————————————
    版权声明：本文为CSDN博主「mangues」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
    原文链接：https://blog.csdn.net/u012915455/java/article/details/80080005
    ```

2. 修改ssh配置`sudo vim /etc/ssh/sshd_config`。

    ```shell
    AllowUsers mangues mangues@192.168.0.1    //允许 mangues 和从 192.168.0.1 登录的 mangues 帐户通过 SSH 登录系统。
    DenyUsers mangues  // 黑名单
    ```

3. `/etc/hosts.allow`和`/etc/hosts.deny`这两个文件。
4. 将用户放进sudoers中，`sudo usermod -a -G sudo hduser`和`sudo adduser ostechnix sudo`。
5. 移除用户的sudo权限，`sudo deluser ostechnix sudo`。

### 4.4. 查看上方使用私钥登录，取消密码登录（安全第三步）

1. 在`/etc/ssh/sshd_config`配置文件中设置以下参数`PasswordAuthentication  no`。

### 4.5. 禁止root用户直接登录ssh（安全第四步）

1. 在`/etc/ssh/sshd_config`配置文件中设置以下参数`PermitRootLogin  no`。
2. 禁用 root 用户远程登录, root登录时可能报用户名或者密码错误。如果您需要使用root进行远程登录, 需要通过其他方式(如vnc)连接到服务器.

### 4.6. ssh隧道

1. `ssh -Nfg -L [本地主机]:[本地端口]:[目标机地址]:[目标机端口] [中间机IP]`
2. 这里我们用到了SSH客户端的三个参数，下面我们一一做出解释：

    ```shelll
    -N 告诉SSH客户端，这个连接不需要执行任何命令，仅仅做端口转发
    -f 告诉SSH客户端在后台运行
    -L 做本地映射端口
    -g 指定该选项表示允许外界主机连接本地转发端口
    ```

### 4.7. 连接问题排查方法

```shell
1. 停止sshd服务
service sshd stop
2. 前台运行sshd,可以看到连接时的打印信息
/usr/sbin/sshd -d
```

## 5. windows下工具

1. [cmder](https://cmder.net/)类linux终端，支持很多linux命令
2. [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)小而美
3. 注：putty支持putty私钥，不支持openssh密钥，需要转换。[Unable to use key file "xxx" (OpenSSH SSH-2 private key)](https://blog.csdn.net/icbm/article/details/89391093)

### 5.1. webshell

1. [huashengdun/webssh](https://github.com/huashengdun/webssh)功能较简单;
2. [liftoff/GateOne](https://github.com/liftoff/GateOne)功能强大，代码也很大。

### 5.2. [HAProxy](https://www.haproxy.org/)

1. [让一个端口同时做两件事：http/https和ssh](https://blog.csdn.net/muye0503/article/details/45721411)

## 6. 系统管理工具

### 6.1. [webmin](http://www.webmin.com/)

1. ubuntu上下载deb文件之后，直接安装即开始运行，可以通过`https://localhost:10000`访问。

## 7. 常用工具

### 7.1. envsubst  将环境变量传递给文件

## 8. 常见问题

1. [ubuntu-18.04 设置开机启动脚本](https://www.cnblogs.com/airdot/p/9688530.html)
2. [Ubuntu 18.04 Server 设置静态IP](https://www.cnblogs.com/jianxuanbing/p/10042892.html)
3. [Ubuntu 16.04 DNS 修改 /etc/resolv.conf 被清空 或重启不生效解决](https://blog.csdn.net/nick19860328/article/details/53533931)
4. [Ubuntu 18.04设置dns](https://www.cnblogs.com/breezey/p/9155988.html)
5. [Ubuntu安装软件出现依赖问题]一般是由于依赖老版本，但是已经安装了新版本，此时可以使用`apitude`工具，不接受推荐，使用降低版本。

