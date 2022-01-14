# 自动化运维管理工具


## 1. Ansible

参考[Ansible中文权威指南](http://ansible.com.cn/index.html)
参考[拉勾教育](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=144#/detail/pc?id=3078)

1. Ansible 是基于 Python 语言开发的，只需要在一台普通的服务器上运行即可，不需要在客户端服务器上安装客户端。
2. Ansible 是基于 SSH 远程管理，而 Linux 服务器基本都开启了 SSH 服务，所以 Ansible 不需要为配置工作添加额外的支持。

### 1.1. 配置(/etc/ansible)

#### 1.1.1. hosts文件

1. 定义主机列表

    ```shell
                    // 未分组的机器保留在顶部
    [webservers]    // 组名
    ixdba1.net      // 可以使用域名、主机名、IP（一般用IP）
    ixdba2.net     // 其后还可以用空格跟变量
    192.168.1.2 ansible_ssh_user=breeze ansible_ssh_pass='123456'

    [dbservers]
    db.ixdba1.net
    db.ixdba2.net
    www[01:50].ixdba.net  // 可以使用数字范围
    db[a:f].ixdba.ent   // 可以使用字母范围
    ```

2. 变量

    ```shell
    ansible_ssh_host 被管理主机的真实IP
    ansible_ssh_port 被管理主机的端口
    ansible_ssh_user 被管理主机的用户名
    ansible_ssh_pass 被管理主机的密码
    ansible_ssh_private_key_file 密钥文件路径

    ansible_sudo_pass    使用sudo连接时的密码
    ansible_sudo_exec    指定sudo命令的路径

    ansible_shell_type    shell的类型，默认sh

    ansible_connection    ssh连接的类型

    ansible_[python/perl/ruby]_interpreter    解释器的路径
    ```

#### 1.1.2. ansible.cfg

1. 默认配置，保持默认即可。
2. 一般将`host_key_checking`设置为false，关闭yes/no的确认。

### 1.2. 执行模式

1. `管理机`是安装 Ansible 的机器，`远程主机`是 Ansible 批量操作的对象，可以是一个或一组主机.

#### 1.2.1. 命令行模式（ad-hoc）

1. 一般用于测试、临时应用等场景。
2. 常用参数

    ```shell
    ansible 主机或组(在 /etc/ansible/hosts 里进行指定)  -m 模块名(默认是command模块) -a '模块参数（要在远程机器上执   行的命令）'  ansible参数
    ```

#### 1.2.2. playbook（多个想要执行的任务[上下关联]放到一个 playbook 中）

1. 主用用于正式环境，通过编写 playbook 文件，可实现固定的、批量的对系统或服务进行配置以及维护工作。

##### 1.2.2.1. 文件格式

1. 由 YMAL 语言编写。通过 `ansible-playbook` 命令进行解析。
2. 文件的第一行应该以“---”（三个连字符）开始，表明 YMAL 文件的开始；
3. 在同一行中，# 之后的内容表示注释，类似于 shell 、Python 和 Ruby；
4. YMAL 中的列表元素以“-”开头，然后紧跟着一个空格，后面为元素内容；
5. 同一个列表中的元素应该保持相同的缩进，否则会被当作错误处理；
6. play 中的 hosts、variables、roles、tasks 等对象的表示方法都是键值中间以“:”分隔表示，“:”后面还要增加一个空格。

##### 1.2.2.2. 文件组成

1. playbook 是由一个或多个“play”组成的列表。
2. playbooks 主要有以下四部分构成，分别如下：
    * Target 部分，定义将要执行 playbook 的远程主机组；
    * Variable 部分，定义 playbook 运行时需要使用的变量；
    * Task 部分，定义将要在远程主机上执行的任务列表；
    * Handler 部分，定义 task 执行完成以后需要调用的任务。
3. 示例

```shell
- name: create user    // 功能概述
  hosts: [ip]    // 指定主机
  remote_user: root    // 指定登陆远程主机的用户
  gather_facts: false
  vars:
    user1: testuser    // 定义了一个变量
  tasks:    // 指定了一个任务
   - name: start createuser    // 人物描述
     user: name="{{user1}}"    // 模块： 模块参数

- hosts: hadoophosts
  remote_user: root
  tasks:
   - name: create hadoop user
     user: name=hadoop state=present
   - name: create hadoop directory and chmod/chown
     file: path=/opt/hadoop state=directory mode=0755 owner=hadoop group=hadoop
   - name: synchronize hadoop program
     synchronize: src=/data/hadoop/ dest=/opt/hadoop
   - name: Setting environment variables
     shell: echo "export JAVA_HOME=/usr/jdk" >> /etc/profile

- hosts: 172.16.213.231
  remote_user: root
  tasks:
   - name: modify selinux    // 关闭远程主机的SELINUX
     replace: path=/etc/selinux/config regexp="enforcing" replace=disabled backup=yes

- hosts: 172.16.213.231
  remote_user: root
  tasks:
   - lineinfile: dest=/etc/profile insertafter='ulimit(.*)' line="ulimit -c unlimited"    // 在 /etc/profile 文件中找到以 ulimit 开头的行，并在后面添加一行内容“ulimit -c unlimited”
   - lineinfile: dest=/etc/profile line="export JAVA_HOME=/usr/jdk"    在 /etc/profile 文件的最后添加一个 JAVA_HOME 路径；
   - lineinfile: dest=/etc/selinux/config regexp='SELINUX=(.*)' line='SELINUX=disabled'    修改 /etc/selinux/config 文件中以“SELINUX=”开头的行，将其替换为“SELINUX=disabled”，其实就是关闭 selinux
   - lineinfile: dest=/etc/resolv.conf regexp='search(.*)' state=absent    //  /etc/resolv.conf 文件找查找以 search 开头的行，然后将其删除掉。


- hosts: 172.16.213.231
  remote_user: root
  tasks:
   - name: ps command
     shell: hostname
     register: host_result
   - debug: var=host_result
   - debug: var=host_result.stdout
   - debug: 'msg="output: {{host_result.stdout}}"'
   - set_fact: var1="{{host_result.stdout}}"
   - set_fact: var2="This is a string"
   - debug: msg="{{var1}}  {{var2}}"

- hosts: 172.16.213.231
  remote_user: root
  gather_facts: true
  tasks:
   - name: connection
     shell: echo "connection . {{inventory_hostname}} $(hostname) ." >> /tmp/local.log
     connection: local
   - name: delegate_to
     shell: echo "delegate_to . {{inventory_hostname}} $(hostname) ." >> /tmp/local.log
     delegate_to: localhost
   - name: local_action
     local_action: shell echo "local_action. {{inventory_hostname}} $(hostname)" >> /tmp/local.log

```

###### 1.2.2.2.1. Target(Hosts 和 Users)

1. hosts：用于指定要执行任务的远程主机.
2. remote_user：用于指定在远程主机上执行任务的用户.

###### 1.2.2.2.2. Task

1. 各任务`按次序`逐个在 hosts 中指定所有远程主机上的执行，即在所有远程主机上完成第一个任务后再开始第二个。
2. 在运行自上而下某 playbook 时，如果中途发生错误，则所有已执行任务都将`回滚`，因此在更正 playbook 后需要重新执行一次。

###### 1.2.2.2.3. Handler

1. handlers 和“notify”配合使用.

###### 1.2.2.2.4. tags

1. Ansible 具有幂等性，因此会自动跳过没有变化的部分；但是当一个 playbook 任务比较多时，一个个的判断每个部分是否发生了变化，也需要很长时间。因此，如果确定某些部分没有发生变化，就可以通过 tags 跳过这些代码片断。

##### 1.2.2.3. playbook模块

###### 1.2.2.3.1. unarchive 模块

1. 实现解压缩，也就是将压缩文件解压分发到远程不同节点上。

```shell
src，源文件路径，这个源文件在管理机上；
dest，指定远程主机的文件路径；
mode，设置远程主机上文件权限。
```

###### 1.2.2.3.2. lineinfile、replace 模块

1. 替换模块为 replace 和 lineinfile。
2. replace 模块可以根据指定的正则表达式替换远程主机下某个文件中的内容

    ```shell
    path
    regexp
    replace
    backup
    ```

3. lineinfile，此模块也可以实现 replace 的功能，但 lineinfile 功能更加强大

###### 1.2.2.3.3. register、set_fact、debug 模块

1. 使用 register 选项，可以将当前 task 的输出结果赋值给一个变量.
2. debug 模块，此模块用于在调试中输出信息。

    ```shell
    msg 可以输出自定义信息，并且变量需要双大括号包含起来；
    var 参数只能输出变量，并且不需要双大括号。
    ```

3. set_fact 和 register 的功能很类似，它也可以将 task 输出赋值给变量。set_fact 更像 shell 中变量的赋值方式，可以将某个变量的值赋值给另一个变量，也可以将字符串赋值给变量。

###### 1.2.2.3.4. delegate_to、connection 和 local_action 模块(管理机本地执行)

1. 在管理机本机上执行一些操作，该如何实现呢？其实现的方法有很多，可以通过 delegate_to（任务委派）来实现，也可通过 connection:local 方法，还可通过 local_action 关键字来实现。

#### 1.2.3. Ansible模块

##### 1.2.3.1. commands

`-m command -a '模块参数'`

###### 1.2.3.1.1. 模块参数（常用的shell命令）

1. creates，后跟一个文件名，当远程主机上存在这个文件时，该命令不执行，否则执行；
2. chdir，在执行指令之前，先切换到该指定的目录；
3. removes，后跟一个文件名，当该文件存在时，该选项执行，否则不执行。
4. commands 模块不支持这些特殊字符（“<”、“>”、“|”、“&”等）

##### 1.2.3.2. shell 模块

1. 功能和用法与 command 模块一样，不过 shell 模块执行命令的时候使用的是 /bin/sh，该模块可以执行任何命令。

##### 1.2.3.3. raw 模块和 script 模块

1. raw 模块功能与 command 和 shell 模块类似，shell 能够完成的操作，raw 也都能完成。不同的是，`raw 模块不需要远程主机上的 Python 环境`。
2. 可以使用 raw 模块来装 Python、python-simplejson 等,来支持其他模块。
3. script 模块是将管理端的 `shell 脚本`拷贝到被管理的远程主机上执行，其原理是先将 shell 复制到远程主机，再在远程主机上执行。此模块的执行，不需要远程主机上的 Python 环境。

##### 1.2.3.4. file 模块、copy 模块与 synchronize 模块 fetch模块

1. `file 模块功能强大，主要用于远程主机上的文件或目录操作`.

    ```shell
    force
    group
    mode
    owner
    path
    recurse
    scr
    dest
    state
    ```

2. copy 模块，此模块用来`复制文件到远程主机`。

    ```shell
    owner
    group
    backup
    content
    dest
    directory_mode
    force
    src  如果用"/"结尾，只复制目录里边的内容
    validate 参数，表示在复制之前验证要拷贝的文件是否正确
    ```

3. copy 模块`拷贝小文件`还可以，如果拷贝大文件或者目录的话，速度很慢，不建议使用。
4. fetch 与copy类似，但是功能相反，`把文件从目标主机拷贝到本地`。

    ```shell
    dest 目标目录
    ```

5. synchronize 模块，此模块通过调用 rsync 进行文件或目录同步，同步速度很快，还支持增量同步

    ```shell
    compress
    copy_links
    delete
    src
    dest
    dest_port
    mode  远程主机必须要有rsync命令
    ```

##### 1.2.3.5. cron 模块、yum 模块与 service 模块

1. cron 模块用于管理计划任务

    ```shell
    ansible 172.16.213.233  -m cron -a 'name="job for reboot" special_time=reboot  job="/data/bootservice.    sh" '    此命令执行后，会在 172.16.213.233 的 crontab 中写入“@reboot /data/bootservice.sh”，通过“crontab    -l ”可以查看到
    ansible 172.16.213.233  -m cron -a 'name="yum autoupdate" weekday="6" minute=20 hour=1 user="root"    job="yum -y update"'    每周六的 1:20 分执行"yum -y update"操作
    ansible 172.16.213.233  -m cron -a  'backup="True" name="autobackup" weekday="6" minute=30  hour=1    user="root" job="/home/ixdba/backup.sh"'    每周六的 1:30 分以 root 用户执行 "/home/ixdba/backup.sh" 脚本
    ansible 172.16.213.233  -m cron -a  'name="checkhttp" minute=30 hour=12 user="root" job="/home/ixdba/   check_http.sh" cron_file="check_http_for_ansible" '    会在 /etc/cron.d 创建一个 check_http_for_ansible   文件，表示每天的 12：30 分通过 root 用户执行 /home/ixdba/check_http.sh 脚本
    ansible 172.16.213.233  -m cron  -a  'name="yum  update" state=absent'    删除一个计划任务
    ```

2. yum 模块的使用，此模块用来通过 `yum 包管理器`来管理软件包.

    ```shell
    config_file    yum的配置文件
    disable_gpg_check    关闭gpg_check
    disablerepo    不启用某个源
    enablerepo    启用某个源
    name    软件包的名字2url3本地的rpm包的路径
    state    present\installed\latest\absent\removed
    ```

3. service 模块，此模块用于管理远程主机上的服务，

    ```shell
    enabled
    name
    sleep
    state    started\stopped\restarted\reloaded
    ```

##### 1.2.3.6. setup 模块获取 Ansible facts 信息

1. setup 模块来收集远程主机的系统信息，这些 facts 信息可以直接以变量的形式使用。

    ```shell
    ansible 172.16.213.77 -m setup -a 'filter=ansible_*_mb'    查看主机内存信息
    ansible 172.16.213.77 -m setup -a 'filter=ansible_em[1-2]'    查看接口为 eth0-2 的网卡信息
    ```

2. playbooks 脚本中，经常会用到一个参数 gather_facts，其与该模块相关。gather_facts 默认值为 yes，也就是说，在使用 Ansible 对远程主机执行任何一个 playbook 之前，总会先`通过 setup 模块获取 facts，并将信息暂存在内存中`，直到该 playbook 执行结束为止。

##### 1.2.3.7. user 模块与 group 模块

1. user 模块请求的是 useradd、userdel、usermod 三个指令；group 模块请求的是 groupadd、groupdel、groupmod 三个指令。

```shell
ansible 172.16.213.77 -m user -a "name=usertest1"    创建一个用户 usertest1
ansible 172.16.213.77 -m user -a "name=usertest2 groups=admins,developers"    创建用户 usertest2，并设置附加组。
ansible 172.16.213.77 -m user -a "name=usertest1 state=absent remove=yes"    删除用户 usertest1 的同时，删除用户根目录
ansible 172.16.213.77 -m user -a 'name=usertest2 password="$1$yjJ74Wid$x0QUaaHzA8EwWU2kG6SRB1" ' 批量修改用户密码
```

### 1.3. 大数据运维实战

#### 1.3.1. hosts

#### 1.3.2. ssh

#### 1.3.3. jdk

## 2. Puppet

## 3. Saltstack

