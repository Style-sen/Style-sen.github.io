# Docker-compose编排容器


## 1. 思想

1. 容器间通讯，使用docker内部网络，最好不要映射出来再调用，会有坑。
2. `默认情况下，Compose会为我们的应用创建一个网络，服务的每个容器都会加入该网络中`。这样，容器就可被该网络中的其他容器访问，不仅如此，该容器还能以服务名称作为hostname被其他容器访问。默认情况下，应用程序的网络名称基于Compose的工程名称，而项目名称基于docker-compose.yml所在目录的名称(例如:app_default)。如需修改工程名称，可使用--project-name标识或COMPOSE_PORJECT_NAME环境变量。


## 2. 命令

## 3. 配置

`docker-compose.yml`

### 3.1. version

```
version: '3.7'
```

### 3.2. services:

```
  [server name]:   // 每个服务为一条
    [配置选项]
```
1. 配置选项，如下。

#### 3.2.1. build

```yml
build:
  context: .
  dockerfile: Dockerfile
```

#### 3.2.2. command

1. 覆盖默认命令。

#### 3.2.3. depends_on

1. 用于指定服务依赖，一般是mysql、redis等。
2. 指定了依赖，将会优先于服务创建并启动依赖，也可以使用容器名作为主机名。

#### 3.2.4. deploy

1. Version 3 only.
2. 用来配置服务运行和部署时的参数。
3. 注：`This only takes effect when deploying to a swarm with docker stack deploy, and is ignored by docker-compose up and docker-compose run`.

#### 3.2.5. entrypoint

覆盖默认entrypoint

#### 3.2.6. environment

设置环境变量

```
environment:
  RACK_ENV: development
  SESSION_SECRET:  # 只给定名称的变量会自动获取它在 Compose 主机上的值，可以用来防止泄露不必要的数据

environment:
  - RACK_ENV=development
  - SESSION_SECRET
```

#### 3.2.7. env_file

1. 从文件中获取变量
2. 如果通过 docker-compose -f FILE 指定了模板文件，则 env_file 中路径会基于模板文件路径。
3. 如果有变量名称与 environment 指令冲突，则以后者为准。

```
env_file: .env

env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env


环境变量文件中每一行必须符合格式，支持 # 开头的注释行。
# common.env: Set Rails/Rack environment
RACK_ENV=development
```

#### 3.2.8. external_links

`链接到 docker-compose.yml 外部的容器，甚至 并非 Compose 管理的容器`。参数格式跟 links 类似。

```
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

#### 3.2.9. healthcheck

1. HEALTHCHECK 指令是告诉 Docker 应该如何进行判断容器的状态是否正常，这是 Docker 1.12 引入的新指令。在没有 HEALTHCHECK 指令前，Docker 引擎只可以通过容器内主进程是否退出来判断容器是否状态异常。很多情况下这没问题，但是如果程序进入死锁状态，或者死循环状态，应用进程并不退出，但是该容器已经无法提供服务了。在 1.12 以前，Docker 不会检测到容器的这种状态，从而不会重新调度，导致可能会有部分容器已经无法提供服务了却还在接受用户请求。
2. 而自 1.12 之后，Docker 提供了 HEALTHCHECK 指令，通过该指令指定一行命令，用这行命令来判断容器主进程的服务状态是否还正常，从而比较真实的反应容器实际状态。
3. 当在一个镜像指定了 HEALTHCHECK 指令后，用其启动容器，初始状态会为 starting，在 HEALTHCHECK 指令检查成功后变为 healthy，如果连续一定次数失败，则会变为 unhealthy。
4. 因此我们需要使用 HEALTHCHECK 从应用的业务层面检查容器的状态。

```
healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9000/minio/health/live"]
      interval: 30s    # 两次健康检查的间隔，默认为 30 秒，单位(h/m/s)；
      timeout: 20s    # 健康检查命令运行超时时间，如果超过这个时间，本次健康检查就被视为失败，默认 30 秒，单位(h/m/s)；
      retries: 3    # 当连续失败指定次数后，则将容器状态视为 unhealthy，默认 3 次。
CMD, ENTRYPOINT 一样，HEALTHCHECK 只可以出现一次，如果写了多个，只有最后一个生效。
```

#### 3.2.10. hostname

1. `通过指定hostname，/etc/hosts中的记录，不再是hash值，而是hostname。`

#### 3.2.11. image


#### 3.2.12. links
1. `允许在连接其他服务时定义别名`。
2. 注意：`They are not required to enable services to communicate - by default, any service can reach any other service at that service’s name.`
```
links:
 - db
 - db:mysql
 - redis

# 使用了别名将自动会在容器的/etc/hosts文件里创建相应记录：
172.17.2.186  db
172.17.2.186  mysql
172.17.2.187  redis
# 所以我们在容器里就可以直接使用别名作为服务的主机名。
```
3. link指定的服务名，必须在此compose配置文件中定义。`ERROR: Service 'nginx_fe' has a link to service 'dc_api:gym_dc_api' which is undefined.`
#### 3.2.13. networks
1. 指定加入的网络.

#### 3.2.14. volumes

将本机的文件夹挂载到container中.
注：此时可能会出现，此文件夹的所有者和组都为`1000`.

#### 3.2.15. user

1. 指定用户。

### 3.3. networks

1. 配置集群的网络。

#### 3.3.1. 配置默认网络

1. `default默认网络，compose启动就会创建`。

```
networks:
  default:
    # Use a custom driver
    driver: custom-driver-1   # host, bridge, overlay
```

#### 3.3.2. 增加新的网络进行网络隔离

```
networks:
  [定义不是default的名称，即创建一个新的网络，default依然存在]
```

#### 3.3.3. 使用已存在的网络

```
networks:
  default:
    external:
      name: my-pre-existing-network
```

#### 3.3.4. 通过上边的组合可以将不同docker-compose中的服务放进一个网络中

### 3.4. volumes

#### 3.4.1. 非service中volumes（即namedvolumes）

```yml
volumes:
  [named volumes]:
    external:
      name: actual-name-of-volume   #表示使用外部创建的卷
```

1. 冒号后边为空。会默认挂载在/var/lib/docker/volumes/。`但是后边不能跟主机路径`
2. 不能指定主机本地路径。

#### 3.4.2. service中的volumes

```yml
volumes:
  # Just specify a path and let the Engine create a volume
  - /var/lib/mysql

  # 使用绝对路径
  - /opt/data:/var/lib/mysql

  # 相对于the Compose file的相对路径
  - ./cache:/tmp/cache

  # User-relative path
  - ~/configs:/etc/configs/:ro

  # 命名过的卷
  - datavolume:/var/lib/mysql
```

