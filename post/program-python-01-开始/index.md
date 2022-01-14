# 开始一个python应用


## 1. 版本切换

[pyenv](https://github.com/pyenv/pyenv)

| 命令             | 功能                      |
| ---------------- | ------------------------- |
| pyenv versions   | 列出已安装python版本      |
| pyenv local      | 设置当前shell的python版本 |
| pyenv global     | 设置全局的python版本      |
| pyenv install -l | 列出可以使用的python版本  |

### 1.1. 安装

1. 安装[依赖](https://github.com/pyenv/pyenv/wiki/Common-build-problems);
2. `curl https://pyenv.run | bash`.

### 1.2. 插件

#### 1.2.1. [pyenv/pyenv-virtualenv](https://github.com/pyenv/pyenv-virtualenv)

1. `pyenv-virtualenv`pyenv插件provides features to manage virtualenvs and conda environments for Python on UNIX-like systems.

```shell
pyenv virtualenv 2.7.10 my-virtual-env-2.7.10  # 指定python版本和安装目录，如果不指定python版本，则使用当前的python版本
pyenv activate <name>
pyenv deactivate
pyenv uninstall my-virtual-env
pyenv virtualenv-delete my-virtual-env
```

## 2. 虚拟环境

### 2.1. venv模块(python3.3以后的自带模块，是virtualenv的一个子集)

`只能创建python同版本的虚拟环境`

### 2.2. [virtualenv](https://github.com/pypa/virtualenv)

将一个目录建立为一个虚拟的python环境，可以建立多个虚拟环境，每个环境里面的python版本可以是不同的，也可以是相同的，而且环境之间相互独立。

```shell
virtualenv [创建虚拟环境的目录]  # 新建一个虚拟环境的目录
```

## 3. 开发必备

### 3.1. ipython

### 3.2. jupyter

### 3.3. pip

## 4. 编码规范

## 5. 开始一个项目

