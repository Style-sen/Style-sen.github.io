# 命令行终端


## 1. 阅读[What's the difference between a console, a terminal, and a shell?](https://www.hanselman.com/blog/whats-the-difference-between-a-console-a-terminal-and-a-shell)

1. `Terminal`只是显示文本并接收输入，他不会看你的输入，也不会思考。
2. 在技术上，`Console`是一个设备，`Terminal`是一个软件；在软件世界中,两者是一个东西。
3. `Shell`是`Terminal`向其发送用户输入的程序。`Shell`生成输出并将其传回`Terminal`显示。
4. 您对`Shell`的选择不会也不应该决定您对`Terminal`的选择.
5. `WSL2`装载一个真正的`Linux kernel`运行在`Windows`之上.`Cygwin`只是让你能够运行你的bash脚本，并不是一个linux，不能运行其他linux下的程序。
6. `Windows`的新`ConPTY`界面是`Windows`上控制台和终端的未来.

## 2. Terminal

### 2.1. Windows Terminal

1. [Windows Terminal 完美配置 PowerShell 7.1](https://zhuanlan.zhihu.com/p/137595941)

```shell
Ctrl+Shift+T #打开一个新标签页
Alt+Shift+D #拆分窗口
```

## 3. Shell

### 3.1. zsh

#### 3.1.1. oh-my-zsh

##### 3.1.1.1. 插件

1. [zsh-users /zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)Fish-like autosuggestions for zsh .

### 3.2. PowerShell

```shell
$PSVersionTable #查看版本
```

