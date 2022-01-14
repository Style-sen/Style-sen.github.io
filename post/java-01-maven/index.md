# 微服务架构笔记-JAVA-MAVEN


参考[Apache Maven 入门篇](https://www.oracle.com/cn/java/technologies/apache-maven-getting-started-1.html)

## 用来做什么

1. Maven 是一个项目管理和构建自动化工具。
2. Maven 使用惯例优于配置的原则 。
3. 它要求在没有定制之前，所有的项目都有如下的结构：

|目录	|目的|
|--|--|
|${basedir}  |	存放 pom.xml(用于描述项目，配置插件和管理依赖关系。)和所有的子目录 |
|${basedir}/src/main/java	| 项目的 java源代码 |
|${basedir}/src/main/resources	| 项目的资源，比如说 property文件|
|${basedir}/src/test/java	| 项目的测试类，比如说 JUnit代码|
|${basedir}/src/test/resources	| 测试使用的资源|
|${basedir}/target/classes  |编译后 的 classes  |
|${basedir}/target | JAR 文件（默认产生）|
|${basedir}/target/test-classes  |测试class文件  |


4. Maven 默认的本地库是 ~/.m2/repository/，在 Windows 下是 %USER_HOME%\.m2\repository\ 。

### 核心概念

#### POM (Project Object Model)
1. 一个项目所有的配置都放置在 POM 文件中：定义项目的类型、名字，管理依赖关系，定制插件的行为等等。比如说，你可以配置 compiler 插件让它使用 java 1.5 来编译。
2. groupId, artifactId, packaging, version 叫作 maven 坐标，它能唯一的确定一个项目.
```
packing默认是jar类型，
<packaging>pom</packaging>   --------->   父类型都为pom类型
<packaging>jar</packaging>      --------->   内部调用或者是作服务使用
<packaging>war</packaging>    --------->   需要部署的项目
```
3. 大项目一般会分成几个子项目。在这种情况下，每个子项目就会有自己的 POM 文件，然后它们会有一个共同的父项目。这样只要构建父项目就能够构建所有的子项目了。子项目的 POM 会继承父项目的 POM。另外，所有的 POM都继承了一个 Super-POM。Super-POM 设置了一些默认值，比如在第一篇文章中提到的默认的目录结构，默认的插件等等，它遵循了惯例优于配置的原则。[POM 标签大全详解](https://www.runoob.com/maven/maven-pom.html)

4. 运行时候的 POM 要复杂的多。 如果你想看到运行时候的 POM 的全部内容的话，可以运行下面的命令：
```
$mvn help:effective-pom
```
#### Maven 插件
1. 用了 mvn archetype:generate 命令来生成一个项目。那么这里的 archetype:generate 是什么意思呢？archetype 是一个插件的名字，generate是目标(goal)的名字。这个命令的意思是告诉 maven 执行 archetype 插件的 generate 目标。插件目标通常会写成 pluginId:goalId.
2. 一个目标是一个工作单元，而插件则是一个或者多个目标的集合。

##### [spotify/docker-maven-plugin](https://github.com/spotify/docker-maven-plugin)

#### Maven 生命周期
1. 第二个命令是：mvn package。这里的 package 是一个maven的生命周期阶段 (lifecycle phase )。生命周期指项目的构建过程，它包含了一系列的有序的阶段 (phase)，而一个阶段就是构建过程中的一个步骤。
2. 插件目标可以绑定到生命周期阶段上。一个生命周期阶段可以绑定多个插件目标。当 maven 在构建过程中逐步的通过每个阶段时，会执行该阶段所有的插件目标。
3. 默认的生命周期
```
process-resources 阶段：resources:resources
compile 阶段：compiler:compile
process-classes 阶段：(默认无目标)
process-test-resources 阶段：resources:testResources
test-compile 阶段：compiler:testCompile
test 阶段：surefire:test
prepare-package 阶段：(默认无目标)
package 阶段：jar:jar
```

#### Maven 依赖管理
1. maven 提供了传递依赖的特性。所谓传递依赖是指 maven 会检查被依赖的 jar 文件，把它的依赖关系纳入最终解决的依赖关系链中。
2. scope 决定了依赖关系的适用范围。

#### Maven 仓库
1. 寻找顺序：本地仓库>中央仓库>远程仓库>报错。

##### 本地仓库
1. `mvn install`把我们的 helloworld 项目安装到本地库.
2. 一个项目被安装到了本地库后，你别的项目就可以通过 maven 坐标和这个项目建立依赖关系。

##### 中央仓库
1. Maven 中央仓库是由 Maven 社区提供的仓库，其中包含了大量常用的库。

##### 远程仓库
1. 开发人员自己定制仓库，包含了所需要的代码库或者其他工程中用到的 jar 文件。

## 开始使用

### 安装
1. `$ sudo update-alternatives --config java`确定JAVA的安装位置。
2.  在`/etc/environment`文件中追加`JAVA_HOME`.

### 建立一个项目

```
mvn archetype:generate -DgroupId=com.mycompany.helloworld -DartifactId=helloworld -Dpackage=com.mycompany.helloworld -Dversion=1.0-SNAPSHOT
```

1. archetype:generate 目标会列出一系列的 archetype 让你选择.

### 先构建

```
mvn package
```

### 后运行

```
java -jar target/gs-rest-service-0.1.0.jar

java -cp target/helloworld-1.0-SNAPSHOT.jar com.mycompany.helloworld.App
```

