# GIT基本操作


## 1. 增加/删除文件

1. `git add -p`添加每个变化前，都会要求确认；对于同一个文件的多处变化，可以实现分次提交；

## 2. 查看更改

1. `git status`查看文件在工作区（Workspace）与本地仓库(Repository)以及暂存区(Index/Stage)与本地仓库(Repository)之间的变化。
2. `git diff`查看工作区与暂存区的差异。
3. `git diff --cached`查看暂存区与本地仓库的区别。
4. `git diff HEAD`查看工作区与本地仓库之间的差异。
5. `git show [提交ID]`查看某一次提交修改的内容。
6. `git log -- [文件名]`按照文件查找日志。

## 3. 版本管理

1. `git describe --tags --always --dirty="-dev"`生成基于上一个tag的版本号。

## git子模块

1. `git submodule foreach`它能在每一个子模块中运行任意命令.

