# 使用GitLab CI/CD创建Docker镜像


翻译自[Building Docker images with GitLab CI/CD](https://docs.gitlab.com/ce/ci/docker/using_docker_build.html)

1. GitLab CI/CD允许使用Docker引擎构建和测试基于Docker的项目。

**Tip:**允许使用`docker-compose`和其他Docker编排工具。

2. 持续集成/部署的趋势之一:

    1. 创建应用镜像
    1. 对所创建的镜像进行测试
    1. 将镜像推到远程registry
    1. 部署到服务器

3. 应用中可包含`Dockerfile`用来创建和测试镜像:

```bash
docker build -t my-image dockerfiles/
docker run my-docker-image /script/to/run/tests
docker tag my-image my-registry:5000/my-image
docker push my-registry:5000/my-image
```

4. GitLab Runner需要一些特别的配置使jobs中支持`docker`。

## Runner配置

1. 有三种方法在jobs中使用`docker build`和`docker run`，各有利弊。

### 使用shell执行器

1. 最简单的方法是将GitLab Runner安装成shell执行器模式。
2. `GitLab Runner`将以`gitlab-runner`用户执行jobs中的脚本.

#### 安装 [GitLab Runner](https://gitlab.com/gitlab-org/gitlab-runner/#installation)

1. 在安装过程中选择 `shell`:

    ```bash
    sudo gitlab-runner register -n \
      --url https://gitlab.com/ \
      --registration-token REGISTRATION_TOKEN \
      --executor shell \
      --description "My Runner"
    ```

1. 在服务器中安装 Docker.

    更多信息参考[Supported installations](https://docs.docker.com/engine/installation/).

1. 将`gitlab-runner`用户加入到`docker`组中:

    ```bash
    sudo usermod -aG docker gitlab-runner
    ```

1. 检查`gitlab-runner`是否可以访问Docker:

    ```bash
    sudo -u gitlab-runner -H docker info
    ```

    将`docker info`写入`.gitlab-ci.yml`来检测一切是否OK:

    ```yaml
    before_script:
      - docker info

    build_image:
      script:
        - docker build -t my-docker-image .
        - docker run my-docker-image /script/to/run/tests
    ```

1. 现在，可以使用`docker`命令和安装`docker-compose`.

**Note:**通过将`gitlab-runner`添加到`docker`组，可以有效地授予`gitlab-runner`完全的根用户权限。更多信息可参考[On Docker security: `docker` group considered harmful](https://www.andreas-jung.com/contents/on-docker-security-docker-group-considered-harmful).

### 使用docker-in-docker执行器

1. 第二种方法是使用docker-in-docker (dind)[Docker image](https://hub.docker.com/_/docker/) 安装有(`docker` and `docker-compose`)，以特权模式在镜像的上下文中执行jobs脚本或命令。

#### 步骤如下

1. 安装[GitLab Runner](https://docs.gitlab.com/runner/install).

1. 命令行注册`GitLab Runner` 使用 `docker` and `privileged`模式:

    ```bash
    sudo gitlab-runner register -n \
      --url https://gitlab.com/ \
      --registration-token REGISTRATION_TOKEN \
      --executor docker \
      --description "My Docker Runner" \
      --docker-image "docker:stable" \
      --docker-privileged
    ```

2. 上边的命令将注册一个新的Runner使用`docker:stable`镜像（Docker提供）. 
    **注意：使用`privileged`模式来执行build和service容器.** 如果要使用[docker-in-docker]模式, 必须在你的Docker容器中使用`privileged = true`。

3. 上边的命令将创建`config.toml`如下:

    ```
    [[runners]]
      url = "https://gitlab.com/"
      token = TOKEN
      executor = "docker"
      [runners.docker]
        tls_verify = false
        image = "docker:stable"
        privileged = true
        disable_cache = false
        volumes = ["/cache"]
      [runners.cache]
        Insecure = false
    ```

1. 现在可以在构建脚本中使用 `docker` 命令 (注意需要包含`docker:dind`服务):

    ```yaml
    image: docker:stable

    variables:
      # When using dind service we need to instruct docker, to talk with the
      # daemon started inside of the service. The daemon is available with
      # a network connection instead of the default /var/run/docker.sock socket.
      #
      # The 'docker' hostname is the alias of the service container as described at
      # https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#accessing-the-services
      #
      # Note that if you're using Kubernetes executor, the variable should be set to
      # tcp://localhost:2375 because of how Kubernetes executor connects services
      # to the job container
      DOCKER_HOST: tcp://docker:2375/
      # When using dind, it's wise to use the overlayfs driver for
      # improved performance.
      DOCKER_DRIVER: overlay2

    services:
      - docker:dind

    before_script:
      - docker info

    build:
      stage: build
      script:
        - docker build -t my-docker-image .
        - docker run my-docker-image /script/to/run/tests
    ```

Docker-in-Docker以上为推荐的配置，工作OK。还有一些额外的配置:

- By enabling `--docker-privileged`, you are effectively disabling all of
  the security mechanisms of containers and exposing your host to privilege
  escalation which can lead to container breakout. For more information, check
  out the official Docker documentation on
  [Runtime privilege and Linux capabilities][docker-cap].
- 当使用docker-in-docker, 每一个job都运行在干净的环境中不包含历史. 并行jobs也会工作得很好因为每一个构建都会获得他们自己的Docker引擎实例，彼此之间不会冲突。但是，这也意味着jobs会运行
很慢，没有layers的缓存。
- 默认情况下， `docker:dind` 使用 `--storage-driver vfs` 是最慢. 可以使用另外一种storage driver[Using the overlayfs driver](#using-the-overlayfs-driver).
- 因为`docker:dind`容器和runner容器没有共享他们的root文件系统, job的工作目录会被挂载在子容器中。 For example, if you have files you want to share with a
  child container, you may create a subdirectory under `/builds/$CI_PROJECT_PATH`
  and use it as your mount point (for a more thorough explanation, check [issue#41227](https://gitlab.com/gitlab-org/gitlab-ce/issues/41227)):

    ```yaml
    variables:
      MOUNT_POINT: /builds/$CI_PROJECT_PATH/mnt

    script:
      - mkdir -p "$MOUNT_POINT"
      - docker run -v "$MOUNT_POINT:/mnt" my-docker-image
    ```

这种方法的一个样例: https://gitlab.com/gitlab-examples/docker.

### 使用 Docker socket 绑定

第三种方法将 `/var/run/docker.sock` 绑定到容器，这样在image中就可以使用docker了.

步骤如下:

1. 安装 [GitLab Runner](https://docs.gitlab.com/runner/install).

1. 命令行注册 GitLab Runner 使用 `docker` 和分享 `/var/run/docker.sock`:

    ```bash
    sudo gitlab-runner register -n \
      --url https://gitlab.com/ \
      --registration-token REGISTRATION_TOKEN \
      --executor docker \
      --description "My Docker Runner" \
      --docker-image "docker:stable" \
      --docker-volumes /var/run/docker.sock:/var/run/docker.sock
    ```

    The above command will register a new Runner to use the special
    `docker:stable` image which is provided by Docker. **Notice that it's using
    the Docker daemon of the Runner itself, and any containers spawned by docker
    commands will be siblings of the Runner rather than children of the runner.**
    This may have complications and limitations that are unsuitable for your workflow.

    The above command will create a `config.toml` entry similar to this:

    ```
    [[runners]]
      url = "https://gitlab.com/"
      token = REGISTRATION_TOKEN
      executor = "docker"
      [runners.docker]
        tls_verify = false
        image = "docker:stable"
        privileged = false
        disable_cache = false
        volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
      [runners.cache]
        Insecure = false
    ```

1. You can now use `docker` in the build script (note that you don't need to
   include the `docker:dind` service as when using the Docker in Docker executor):

    ```yaml
    image: docker:stable

    before_script:
      - docker info

    build:
      stage: build
      script:
        - docker build -t my-docker-image .
        - docker run my-docker-image /script/to/run/tests
    ```

While the above method avoids using Docker in privileged mode, you should be
aware of the following implications:

- By sharing the docker daemon, you are effectively disabling all
  the security mechanisms of containers and exposing your host to privilege
  escalation which can lead to container breakout. For example, if a project
  ran `docker rm -f $(docker ps -a -q)` it would remove the GitLab Runner
  containers.
- Concurrent jobs may not work; if your tests
  create containers with specific names, they may conflict with each other.
- Sharing files and directories from the source repo into containers may not
  work as expected since volume mounting is done in the context of the host
  machine, not the build container, e.g.:

    ```
    docker run --rm -t -i -v $(pwd)/src:/home/app/src test-image:latest run_app_tests
    ```

## Making docker-in-docker builds faster with Docker layer caching

When using docker-in-docker, Docker will download all layers of your image every
time you create a build. Recent versions of Docker (Docker 1.13 and above) can
use a pre-existing image as a cache during the `docker build` step, considerably
speeding up the build process.

### How Docker caching works

When running `docker build`, each command in `Dockerfile` results in a layer.
These layers are kept around as a cache and can be reused if there haven't been
any changes. Change in one layer causes all subsequent layers to be recreated.

You can specify a tagged image to be used as a cache source for the `docker build`
command by using the `--cache-from` argument. Multiple images can be specified
as a cache source by using multiple `--cache-from` arguments. Keep in mind that
any image that's used with the `--cache-from` argument must first be pulled
(using `docker pull`) before it can be used as a cache source.

### Using Docker caching

Here's a simple `.gitlab-ci.yml` file showing how Docker caching can be utilized:

```yaml
image: docker:stable

services:
  - docker:dind

variables:
  CONTAINER_IMAGE: registry.gitlab.com/$CI_PROJECT_PATH
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.gitlab.com

build:
  stage: build
  script:
    - docker pull $CONTAINER_IMAGE:latest || true
    - docker build --cache-from $CONTAINER_IMAGE:latest --tag $CONTAINER_IMAGE:$CI_COMMIT_SHA --tag $CONTAINER_IMAGE:latest .
    - docker push $CONTAINER_IMAGE:$CI_COMMIT_SHA
    - docker push $CONTAINER_IMAGE:latest
```

The steps in the `script` section for the `build` stage can be summed up to:

1. The first command tries to pull the image from the registry so that it can be
   used as a cache for the `docker build` command.
1. The second command builds a Docker image using the pulled image as a
   cache (notice the `--cache-from $CONTAINER_IMAGE:latest` argument) if
   available, and tags it.
1. The last two commands push the tagged Docker images to the container registry
   so that they may also be used as cache for subsequent builds.

## Using the OverlayFS driver

NOTE: **Note:**
The shared Runners on GitLab.com use the `overlay2` driver by default.

By default, when using `docker:dind`, Docker uses the `vfs` storage driver which
copies the filesystem on every run. This is a very disk-intensive operation
which can be avoided if a different driver is used, for example `overlay2`.

### Requirements

1. Make sure a recent kernel is used, preferably `>= 4.2`.
1. Check whether the `overlay` module is loaded:

    ```
    sudo lsmod | grep overlay
    ```

    If you see no result, then it isn't loaded. To load it use:

    ```
    sudo modprobe overlay
    ```

    If everything went fine, you need to make sure module is loaded on reboot.
    On Ubuntu systems, this is done by editing `/etc/modules`. Just add the
    following line into it:

    ```
    overlay
    ```

### Use driver per project

You can enable the driver for each project individually by editing the project's `.gitlab-ci.yml`:

```
variables:
  DOCKER_DRIVER: overlay2
```

### Use driver for every project

To enable the driver for every project, you can set the environment variable for every build by adding `environment` in the `[[runners]]` section of `config.toml`:

```toml
environment = ["DOCKER_DRIVER=overlay2"]
```

If you're running multiple Runners you will have to modify all configuration files.

> **Notes:**
>
> - More information about the Runner configuration is available in the [Runner documentation](https://docs.gitlab.com/runner/configuration/).
> - For more information about using OverlayFS with Docker, you can read
>   [Use the OverlayFS storage driver](https://docs.docker.com/engine/userguide/storagedriver/overlayfs-driver/).

## Using the GitLab Container Registry

> **Notes:**
> - This feature requires GitLab 8.8 and GitLab Runner 1.2.
> - Starting from GitLab 8.12, if you have [2FA] enabled in your account, you need
>   to pass a [personal access token][pat] instead of your password in order to
>   login to GitLab's Container Registry.

Once you've built a Docker image, you can push it up to the built-in
[GitLab Container Registry](../../user/project/container_registry.md).
Some things you should be aware of:

- You must [log in to the container registry](#authenticating-to-the-container-registry)
  before running commands. You can do this in the `before_script` if multiple
  jobs depend on it.
- Using `docker build --pull` fetches any changes to base
  images before building just in case your cache is stale. It takes slightly
  longer, but means you don’t get stuck without security patches to base images.
- Doing an explicit `docker pull` before each `docker run` fetches
  the latest image that was just built. This is especially important if you are
  using multiple runners that cache images locally. Using the git SHA in your
  image tag makes this less necessary since each job will be unique and you
  shouldn't ever have a stale image. However, it's still possible to have a
  stale image if you re-build a given commit after a dependency has changed.
- You don't want to build directly to `latest` tag in case there are multiple jobs
  happening simultaneously.

### Authenticating to the Container Registry

There are three ways to authenticate to the Container Registry via GitLab CI/CD
and depend on the visibility of your project.

For all projects, mostly suitable for public ones:

- **Using the special `gitlab-ci-token` user**: This user is created for you in order to
  push to the Registry connected to your project. Its password is automatically
  set with the `$CI_JOB_TOKEN` variable. This allows you to automate building and deploying
  your Docker images and has read/write access to the Registry. This is ephemeral,
  so it's only valid for one job. You can use the following example as-is:

    ```sh
    docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY
    ```

For private and internal projects:

- **Using a personal access token**: You can create and use a
  [personal access token](../../user/profile/personal_access_tokens.md)
  in case your project is private:
    - For read (pull) access, the scope should be `read_registry`.
    - For read/write (pull/push) access, use `api`.
  Replace the `<username>` and `<access_token>` in the following example:

    ```sh
    docker login -u <username> -p <access_token> $CI_REGISTRY
    ```

- **Using the GitLab Deploy Token**: You can create and use a
  [special deploy token](../../user/project/deploy_tokens/index.md#gitlab-deploy-token)
  with your private projects. It provides read-only (pull) access to the Registry.
  Once created, you can use the special environment variables, and GitLab CI/CD
  will fill them in for you. You can use the following example as-is:

    ```sh
    docker login -u $CI_DEPLOY_USER -p $CI_DEPLOY_PASSWORD $CI_REGISTRY
    ```

### Container Registry examples

If you're using docker-in-docker on your Runners, this is how your `.gitlab-ci.yml`
could look like:

```yaml
 build:
   image: docker:stable
   services:
     - docker:dind
   variables:
     DOCKER_HOST: tcp://docker:2375
     DOCKER_DRIVER: overlay2
   stage: build
   script:
     - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.example.com
     - docker build -t registry.example.com/group/project/image:latest .
     - docker push registry.example.com/group/project/image:latest
```

You can also make use of [other variables](../variables/README.md) to avoid hardcoding:

```yaml
services:
  - docker:dind

variables:
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN $CI_REGISTRY

build:
  stage: build
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
```

Here, `$CI_REGISTRY_IMAGE` would be resolved to the address of the registry tied
to this project. Since `$CI_COMMIT_REF_NAME` resolves to the branch or tag name,
and your branch-name can contain forward slashes (e.g., feature/my-feature), it is
safer to use `$CI_COMMIT_REF_SLUG` as the image tag. This is due to that image tags
cannot contain forward slashes. We also declare our own variable, `$IMAGE_TAG`,
combining the two to save us some typing in the `script` section.

Here's a more elaborate example that splits up the tasks into 4 pipeline stages,
including two tests that run in parallel. The `build` is stored in the container
registry and used by subsequent stages, downloading the image
when needed. Changes to `master` also get tagged as `latest` and deployed using
an application-specific deploy script:

```yaml
image: docker:stable
services:
  - docker:dind

stages:
  - build
  - test
  - release
  - deploy

variables:
  DOCKER_HOST: tcp://docker:2375
  DOCKER_DRIVER: overlay2
  CONTAINER_TEST_IMAGE: registry.example.com/my-group/my-project/my-image:$CI_COMMIT_REF_SLUG
  CONTAINER_RELEASE_IMAGE: registry.example.com/my-group/my-project/my-image:latest

before_script:
  - docker login -u gitlab-ci-token -p $CI_JOB_TOKEN registry.example.com

build:
  stage: build
  script:
    - docker build --pull -t $CONTAINER_TEST_IMAGE .
    - docker push $CONTAINER_TEST_IMAGE

test1:
  stage: test
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker run $CONTAINER_TEST_IMAGE /script/to/run/tests

test2:
  stage: test
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker run $CONTAINER_TEST_IMAGE /script/to/run/another/test

release-image:
  stage: release
  script:
    - docker pull $CONTAINER_TEST_IMAGE
    - docker tag $CONTAINER_TEST_IMAGE $CONTAINER_RELEASE_IMAGE
    - docker push $CONTAINER_RELEASE_IMAGE
  only:
    - master

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - master
```

[docker-in-docker]: https://blog.docker.com/2013/09/docker-can-now-run-within-docker/
[docker-cap]: https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities
[2fa]: ../../user/profile/account/two_factor_authentication.md
[pat]: ../../user/profile/personal_access_tokens.md
