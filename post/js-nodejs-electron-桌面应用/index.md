# 使用Electron创建桌面应用


[Electron API 演示(中文版)](https://github.com/demopark/electron-api-demos-Zh_CN)

## 完成一个桌面项目

### 使用Xmind制作功能描述和模块划分

### 使用pencil制作界面原型

### 使用yed制作流程图

### 开始一个工程

*注：创建一个新的空的工程之后，跑通整个流程（打包之后可运行，可CI/CD）之后，再开始写业务代码。*

#### 参考项目[NordicSemiconductor/pc-nrfconnect-core](https://github.com/NordicSemiconductor/pc-nrfconnect-core.git)


#### [electron-vue](https://simulatedgreg.gitbooks.io/electron-vue/content/cn/)    
基于 vue (基本上是它听起来的样子) 来构造 electron 应用程序的样板代码。

##### 项目结构

###### webpack配置

`.electron-vue/webpack.renderer.config.js`
针对 electron 的 renderer 进程。此配置用来处理你的 Vue 应用程序，因此它包含 vue-loader 和许多其他可在官方 vuejs-templates/webpack 样板中找到的配置。

白名单里的外部组件
一个关于此配置的重要的事情是，你可以将特定的模块列入白名单，而不是把它视为 webpack 的 externals。并没有很多情况需要这个功能，但在某些情况下，对于提供原始的 *.vue 组件的 Vue UI 库，他们需要被列入白名单，以至于 vue-loader 能够编译它们。另一个使用情况是使用 webpack 的 alias，例如设置 vue 来导入完整的 编译+运行环境 的构建。因此，vue 已经在白名单中了。
例如`element-ui`。


##### 打包分发

1. 参考[如何深入理解 electron-vue 的 build 构建命令](https://newsn.net/say/electron-vue-build-command.html)
2. 使用electron-builder，配置文件在package.json中的build项中，需要先`node .electron/build.js`,然后`electron-builder`.
3. 使用electron-packager，配置文件在.electron/build.config.js中,packager的调用直接在.electron/build.js中，只需要`node .electron/build.js`.
4. electron-vue构建之后，会有asar文件。[如何查看asar文件内部结构？asar辅助查看工具](https://newsn.net/say/electron-asar-explorer.html)

### 打包分发

#### [electron-builder](https://www.electron.build/)

1. 在CI服务器和开发机器上进行`代码签名`；
2. `自动更新`；
3. 多种目标格式；
4. `Docker`To build Linux or Windows on any platform.

#### 技巧

1. [electron 打包之后，如何使用 F12 开启开发者工具？](https://newsn.net/say/electron-f12.html)

## 疑难杂症

1. 在Electron项目中npm install模块之后，可能会出先版本不匹配的问题，此时需要使用Electron-rebuild重新编译一下。
```
npm install --save-dev electron-rebuild

Every time you run "npm install", run this:
./node_modules/.bin/electron-rebuild

On Windows if you have trouble, try:
.\node_modules.bin\electron-rebuild.cmd
```

2. 上个问题如果出问题可以使用下边的命令：
```
./node_modules/.bin/electron-rebuild -m .
```

3. 上述命令依然失败，删除`～/.electron-gyp`重试。
4. `gyp ERR! stack Error: read ECONNRESET`为网络原因，在比较好的网络下解决。
5. [pc-ble-driver-js test code dfu.js with electron module error](https://devzone.nordicsemi.com/f/nordic-q-a/32559/pc-ble-driver-js-test-code-dfu-js-with-electron-module-error/125707)关于module did not register的问题。
`增加.npmrc在工程目录下解决，都不需要Electron-rebuild`
```
runtime = electron
target = 2.0.17
disturl = https://atom.io/download/electron
```

6. `electron应用中使用了NFC读卡器功能，依赖的是系统的pcsc服务，此时运行代码显示空白，需要一个读卡器硬件辅助，启动读卡器服务，然后打开软件之后，不再是空白。`

7. `No 'Access-Control-Allow-Origin' error on for origin` 跨域访问

```
// main/index.js
mainWindow = new BrowserWindow({
    height: 563,
    useContentSize: true,
    width: 1000,
    webPreferences: { webSecurity: false }
  })
```

## API

### [BrowserWindow](https://electronjs.org/docs/api/browser-window#browserwindowgetfocusedwindow)

1. 创建和控制浏览器窗口
2. 在主进程和渲染进程调用的方式不一样
```
    在主进程中直接调用：const {BrowserWindow} = require('electron')
    在渲染进程中则需使用remote来调用：const {BrowserWindow} = require('electron').remote
```
