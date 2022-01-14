# SCRUM+GITLAB-FLOW


参考[GitLab 工作流概览](https://linux.cn/article-8503-1.html)

GitLab 是一个基于 git 的仓库管理程序，也是一个方便软件开发的强大完整应用。

## 1. 软件开发阶段

**一般情况下，软件开发经过 10 个主要阶段；GitLab 为这 10 个阶段依次提供了解决方案**：

`IDEA`： 每一个从点子开始的项目，通常来源于一次闲聊。在这个阶段，GitLab 集成了 Mattermost。（开始一个项目）
`ISSUE`： 最有效的讨论一个点子的方法，就是为这个点子建立一个工单讨论。你的团队和你的合作伙伴可以在 工单追踪器issue tracker 中帮助你去提升这个点子。
`PLAN`： 一旦讨论得到一致的同意，就是开始编码的时候了。但是等等！首先，我们需要优先考虑组织我们的工作流。对于此，我们可以使用 工单看板Issue Board。（backlog）
`CODE`： 现在，当一切准备就绪，我们可以开始写代码了。
`COMMIT`： 当我们为我们的初步成果欢呼的时候，我们就可以在版本控制下，提交代码到功能分支了。
`TEST`： 通过 GitLab CI，我们可以运行脚本来构建和测试我们的应用。
`REVIEW`： 一旦脚本成功运行，我们测试和构建成功，我们就可以进行 代码复审code review 以及批准。
`STAGING`：： 现在是时候将我们的代码部署到演示环境来检查一下，看看是否一切就像我们预估的那样顺畅——或者我们可能仍然需要修改。
`PRODUCTION`： 当一切都如预期，就是部署到生产环境的时候了！
`FEEDBACK`： 现在是时候返回去看我们项目中需要提升的部分了。我们使用周期分析 Cycle Analytics来对当前项目中关键的部分进行的反馈。

## 2. GitLab 工单追踪器

1. 就是一个sprint backlog。每一个sprint就是一个milestone，一个项目的里程碑可以升级到组中，组中具有相同的里程碑会合并。。
2. 包含了一个backlog看板。

## 3. 通过 GitLab 进行代码复审

1. 在`merge request`页面，可以进行评论，也可以`Insert suggestion`进行代码的建议修改。

## 4. 构建、测试以及发布

[Auto DevOps](https://git.witcent.net/help/topics/autodevops/index.md)

### 4.1. 静态代码测试

#### 4.1.1. [SonarQube](https://www.jianshu.com/p/fdd6fb209f10)

1. 由于 SonarQube 使用 Elasticsearch 作为全文模糊搜索引擎，故需要设置如下内核参数。

    ```shell
    # 查看
    $ sysctl vm.max_map_count # max_map_count文件包含限制一个进程可以拥有的VMA(虚拟内存区域)的数量
    $ sysctl fs.file-max
    $ ulimit -n # 查看文件句柄数
    $ ulimit -u # 查看最大用户进程数

    # 实时修改生效
    $ sysctl -w vm.max_map_count=262144
    $ sysctl -w fs.file-max=65536
    $ ulimit -n 65536
    $ ulimit -u 4096

    # 永久生效
    $ echo "vm.max_map_count=262144 fs.file-max=65536" > /etc/sysctl.d/99-sysctl.conf
    $ /sbin/sysctl -p
    ```

2. 安装运行，使用[官方文档](https://docs.sonarqube.org/latest/setup/install-server/)中的`Docker Compose.yml`作为参考。
3. 首次使用`admin`和`admin`登陆。
4. 安装C++检查工具,在gitlab-runner容器中，安装`apt-get install cppcheck`，不用和sonarqube在一台机器中.
5. 安装插件[‵sonar-cxx-plugin-x.y.z.jar‵](https://github.com/SonarOpenCommunity/sonar-cxx/wiki/Install-the-Plugin)【Sonar C++ plugin只是完成了报告的读取，并没有任何代码分析的功能】.

    ```shell
    1. 下载jar文件，拷贝到SONARQUBE_HOME/extensions/plugins；
    2. 1. 重启sonarqube实例；
    3. 【Administration】->【Marketplace】->【Plugins】查看插件已经安装成功.
    4. 新建一个【Quality Profiles】,选择CXX，并在右上角设置里，设为默认。
    5. 【rules】中选择CXX，然后选择【Repository】选择检查工具,如cppcheck，点击【Bulk Change】应用在自定义的Quality Profiles中。
        clang-tidy 跨平台。
    6. 【重要】需要设置一个选项，【Administration】->【Configuration】-> 【General Settings】-> 【CXX】->【File suffixes】 必须设置。
    7. 【重要】【Administration】->【Configuration】-> 【General Settings】-> 【CXX External Analyzers】需要设置文件名称。
   ```

6. 接下来，有两个选择：a,安装sonar-scanner，手动扫描；b,安装[gitlab-plugin](https://github.com/gabrie-allaigre/sonar-gitlab-plugin)插件，每次提交都会检查一次,但是`Only SonarQube < 7.7, because preview mode is removed`有限制。

    ```markdown
    1. 下载[sonar-scanner](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/);
    2. 配置/etc/profile
        [root@localhost local]# vim /etc/profile
        export SONAR_HOME=/usr/local/sonarqube-6.7.5
        export SONAR_SCANNER_HOME=/usr/local/sonar-scanner
        PATH=$PATH:$SONAR_HOME/bin:$SONAR_RUNNER_HOME/bin
        [root@localhost local]# source /etc/profile
    ```

7. 增加用户，生成token，编写`sonar-project.properties`文件。

    ```yml
    #sonarqube服务器地址
    sonar.host.url=[ip+port]
    sonar.login=[token] #使用token，不用配passwd
    #项目唯一标识（不能出现重复）
    sonar.projectKey=key
    #项目名称
    sonar.projectName=name
    #源代码目录
    sonar.sources=src
    #版本号
    sonar.projectVersion=V1.0.0
    #语言
    sonar.language=CXX
    #源代码文件编码
    sonar.sourceEncoding=UTF-8
    sonar.cxx.clangtidy.reportPath=cppcheck-report.xml
    ```

8. 使用.先执行cppcheck`cppcheck --xml --xml-version=2 --enable=all ./ 2>cppcheck-report.xml`，然后执行`sonar-scanner`.

### 4.2. 发布

#### 4.2.1. 文档发布

1. Gitlab Pages的原理：

    ```markdown
    1. 首先，当你新建一个Gitlab项目的时候，你同时也建了一个存储库。
    2. 只要你的工程目录的根目录下有.gitlab-ci.yml文件，注意必须严格起这个名字。gitlab runner就会执行这个脚本文件。
    3. 脚本的最终目的很简单：把静态文件放入名为public目录下的文件夹内。Gitlab pages会一直去这个存储库的public目录找。注意：这个public是指存储库的public，与项目结构无关。换言之，这个public目录不可见。
    4. 也就是说：当你在项目的根目录下添加了.gitlab-ci.yml，且在脚本中将静态文件正确地放入了public文件夹，gitlab page就会自动打开并部署，部署的域名将会默认分配。
    ```

2. [Gitlab Pages默认是没有使能的，需要在配置文件中打开](https://www.e-learn.cn/topic/491597)。
3. 使用CI配置文件，必须使用`pages`作为job的名称，必须使用`public`文件夹作为附件。

##### 4.2.1.1. Doxygen生成文档放进public中

## 5. 使用gitlab的11条军规

### 5.1. 使用功能分支-不直接提交（commit）到 master 分支

1. 使用 Git 时，任何正在进行的操作，都应为之创建一个分支（branch），以便最终在合并（merge）之前进行代码审查（code review）。

### 5.2. 测试所有的提交-不仅仅只在 master 分支上

1. 有些人将 CI 系统设置为只测试合并到 master 分支的东西。这太迟了！总是拥有绿色的 master 测试（译者注：绿色在 CI 里通常意味着测试通过，而红色意味着测试失败），人们会觉得有信心。例如，在开始开发新功能之前，人们不得不测试 master 分支那是没有意义和荒谬的。CI 并不昂贵，所以这样做是最好的。

### 5.3. 在所有的提交上-运行所有的测试

1. 如果你的测试时间长于 5 分钟则让它们并行；
2. 如果您正工作在一个功能分支并添加新提交（commit），请随之运行测试。如果测试需要很长时间，请尝试并行运行。合并请求（merge requests）时，在服务器端来执行此操作以运行完整的测试套件。如果你有一个用于开发的测试套件，而另一个测试套件你只为新版本运行；那么设置并行测试并运行全部测试套件是值得的。

### 5.4. 在合并到 master 之前执行代码审查，而不是事后审查

1. 不要在一周结束时测试一切。当场就搞！这样你更有可能抓住可能导致问题的东西，而其他人也会努力提出解决方案。

### 5.5. 部署是自动的，并基于分支或标签（tag）

1. 如果您不想每次都部署 master 分支，那么你可以创建一个 production 分支；但你没理由用脚本（script）或登录到某个地方手动操作。自动化一切，或以特定分支来触发生产部署。

### 5.6. 标签（tag）是由用户设置的，而不是由 CI 创建

1. 应由用户来打标签（tag），并且基于此，CI 将执行一个动作。不应让 CI 去更改代码库。如果你需要非常详细的指标，你应该【有一个服务器报告】来详细说明新版本。

### 5.7. 发布（release）是基于标签（tag）的

1. 如果你打了 tag，则意味着创建了一个新版本。

### 5.8. 永远不对已推送的提交（pushed commits）进行变基（rebase）

1. 当你推送（push）到公共分支后，你就不该对其变基，因为这样会很难跟进你正在改进的内容，很难跟进测试结果是什么，而且它打破了 cherry-picking。有时我们在审查流程的后期要求代码贡献者合并及变基（git merge --squash），以使一些东西容易还原时，我们也会违背这一规则。但一般而言，准则是：代码应干净，修改历史应真实。

### 5.9. 每个人都从 master 分支开始工作，目标也是 master 分支

1. 这意味着没有任何长期分支。你可以检出 master 分支，构建功能，创建合并请求，并再次指向到 master 分支。在合并之前，应该进行完整的代码审查，而不应在代码审查和合并间存在任何中间阶段。

### 5.10. 在 master 分支中修正错误，其次再到发布分支

1. 如果你发现 bug，最糟的事莫过于你在刚发布的版本里修复了它，而未在 master 分支修复。为避免这种情况，应总是向前修复（fix forward）。在 master 中修复，然后 cherry-pick 到其他补丁发布分支。

### 5.11. 提交信息（commit message）应体现意图

1. 不仅要说明你做了什么，还应说明为什么这么做。如果解释一下为什么这么做，而不是用其他方式，这会更加有用。

