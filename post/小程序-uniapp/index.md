# UNIAPP


1. 看完这篇白话uni-app。
2. DCloud与vue合作，在vue.js官网提供了免费视频教程，也可以直达教程地址：https://learning.dcloud.io。
3. [学习资源](https://uniapp.dcloud.io/resource)。

## 知识点

1. 全局样式：在根目录下的app.vue里写入，每个页面都会加载app.vue里的样式。
2. 如需要全局导入vue组件，即每个页面都可以直接使用而不用引用和注册的话，在项目根目录下的main.js里处理。如下是hello uni-app里的例子。
```
//main.js  
import pageHead from './components/page-head.vue' //导入  
Vue.component('page-head', pageHead) //注册。注册后在每个vue的page页面里可以直接使用<page-head></page-head>组件。
```
### 组件/标签的变化
1. 以前是html标签，现在是小程序组件。具体说来：
```
div 改成 view
span、font 改成 text
a 改成 navigator
img 改成 image
input 还在，但type属性改成了confirmtype
form、button、checkbox、radio、label、textarea、canvas、video 这些还在。
select 改成 picker
iframe 改成 web-view
ul、li没有了，都用view替代
audio 不再推荐使用，改成api方式，背景音频api文档
```
其实老的HTML标签也可以在uni-app里使用，uni-app编译器会在编译时把老标签转为新标签，比如把div编译成view。但`不推荐这种用法`，调试H5端时容易混乱。
除了改动外，新增了一批手机端常用的新组件
```
scroll-view 可区域滚动视图容器
swiper 可滑动区域视图容器
icon 图标
rich-text 富文本（不可执行js，但可渲染各种文字格式和图片）
progress 进度条
slider 滑块指示器
switch 开关选择器
camera 相机
live-player 直播
map 地图
cover-view 可覆盖原生组件的视图容器 cover-view需要多强调几句，uni-app的非h5端的video、map、canvas、textarea是原生组件，层级高于其他组件。如需覆盖原生组件，比如在map上加个遮罩，则需要使用cover-view组件
```
除了内置组件，还有很多开源的扩展组件，把常用操作都进行封装，DCloud建立了插件市场收录这些扩展组件，详见插件市场

### JS的变化

#### 运行环境变化

1. 在uni-app的各个端中，除了h5端，其他端的js都运行在一个独立的v8引擎下，不是在浏览器中，所以浏览器的对象（浏览器专用的window、document、navigator、location对象，包括cookie等存储）无法使用。
2. app和小程序支持web-view组件，里面可以加载标准HTML，这种页面仍然支持浏览器专用对象window、document、navigator、location。

#### 数据绑定模式变化

1. 现在`前端趋势是去dom化`，改用mvvm模式，更简洁的写法，大幅减少代码行数，同时差量渲染性能更好。

#### api变化

1.  uni-app的api是参考小程序的

```
alert,confirm 改成 uni.showmodel
ajax 改成 uni.request
cookie、session 没有了，local.storage 改成 uni.storage
```
2. uni-app在不同的端，支持条件编译，无限制的使用各端独有的api.

### CSS的变化 

1. *选择器不支持；元素选择器里没有body，改为了page。
2. 单位方面，`px无法动态适应不同宽度的屏幕`，rem无法用于nvue/weex。如果想使用根据屏幕宽度自适应的单位，推荐使用`rpx`，全端支持。
3. uni-app推荐使用flex布局，并默认就是flex布局.
4. 注意背景图和字体文件尽量不要大于40k。会影响性能。如果非要大于40k，需放到服务器侧远程引用或base64后引入，不能放到本地作为独立文件引用。在小程序里，其实小于40k的文件在css里也无法引用，uni-app编译器在编译时自动做了处理，把小于40k的文件编译为base64方式了。

### 工程结构

```
┌─components            uni-app组件目录
│  └─comp-a.vue         可复用的a组件
├─hybrid                存放本地网页的目录，详见
├─platforms             存放各平台专用页面的目录，详见
├─pages                 业务页面文件存放的目录
│  ├─index
│  │  └─index.vue       index页面
│  └─list
│     └─list.vue        list页面
├─static                存放应用引用静态资源（如图片、视频等）的目录，注意：静态资源只能存放于此
├─wxcomponents          存放小程序组件的目录，详见
├─utils                 （非必须，约定）存放自定义模块
├─common                （非必须，约定）存放样式文件
├─api                   （非必须，约定）存放API
├─main.js               Vue初始化入口文件
├─App.vue               应用配置，用来配置App全局样式以及监听应用生命周期
├─manifest.json         配置应用名称、appid、logo、版本等打包信息，详见
├─uni.scss              全局的样式文件
└─pages.json            配置页面路由、导航条、选项卡等页面类信息，详见
```

1. 每个可显示的页面，都必须在 pages.json 中注册。如果你开发过小程序，那么pages.json类似app.json。如果你熟悉vue，这里没有vue的路由，都是在pages.json里管理。
2. uni-app的首页，是在pages.json里配的，`page节点下第一个页面就是首页`。一般在/pages/xx的目录下。
3. app和小程序中，为了提升体验，页面提供了原生的导航栏和底部tabbar，注意这些配置是在pages.json中做，而不是在vue页面里创建，但点击事件的监听在显示的vue页面中做。
4. 对比小程序:
```
1. 原来app.json被一拆为二。页面管理，被挪入了uni-app的pages.json；非页面管理，挪入了manifest.json
2. 原来的app.js和app.wxss被合并到了app.vue中
```

#### pages.json
1. 用来对 uni-app 进行全局配置，决定`页面文件的路径`、`窗口样式`、`原生的导航栏`、`底部的原生tabbar` 等。
2. 类似微信小程序中app.json的页面管理部分。注意`定位权限申请等原属于app.json的内容，在uni-app中是在manifest中配置`。

##### pages

1. 第一个为默认页。

##### tabBar 底部

1. 显示的顺序为list的顺序。

##### globalStyle

#### app.vue

1. onLaunch:可以通过option获取场景值。

### 生命周期


## 框架

## 组件

### 视图容器

#### view 视图容器

#### scroll-view 可滚动视图区域

### 表单组件

#### button


1. @tap 触发

### 扩展组建（uni-ui）


## API

### 界面

#### 交互反馈

##### uni.showToast(OBJECT)

### 设备

#### 蓝牙

##### uni.openBluetoothAdapter
##### uni.startBluetoothDevicesDiscovery
##### uni.onBluetoothDeviceFound
##### uni.stopBluetoothDevicesDiscovery
##### uni.onBluetoothAdapterStateChange
##### uni.getConnectedBluetoothDevices
##### uni.getBluetoothDevices
##### uni.getBluetoothAdapterState
##### uni.closeBluetoothAdapter

#### BLE

##### uni.writeBLECharacteristicValue
##### uni.readBLECharacteristicValue
##### uni.onBLEConnectionStateChange
##### uni.onBLECharacteristicValueChange
##### uni.notifyBLECharacteristicValueChange
##### uni.getBLEDeviceServices
##### uni.getBLEDeviceCharacteristics
##### uni.createBLEConnection
##### uni.closeBLEConnection

## 实战

### 父子组件通信

1. uni.
