# VUE


## VUE实例的生命周期

![生命周期](/images/vue/lifecycle.png)

## VUE实例的特殊属性和方法

### $refs

一个对象，持有注册过 ref 特性 的所有 DOM 元素和组件实例。

## VUE实例属性

```
Vue.prototype.$appName = 'My App'

这样 $appName 就在所有的 Vue 实例（每个页面都是一个独立的实例）中可用了，甚至在实例被创建之前就可以。如果我们运行：

new Vue({
  beforeCreate: function () {
    console.log(this.$appName)
  }
})
则控制台会打印出 My App。就这么简单！
```

## 实战技巧

1. [防止浏览器缓存css,js静态文件](https://blog.csdn.net/akon_vm/article/details/8494579)    
使用此方法可以在程序运行过程中，重新下载同名的静态文件。
2. [Vue导出页面为PDF格式](https://blog.csdn.net/pratise/article/details/79249943)
3. [Vue导出json数据到Excel电子表格](https://segmentfault.com/a/1190000012117303)



## 安装

### 使用[Vue CLI](https://cli.vuejs.org/zh/)

1. 可图形界面创建VUE项目(vue ci)。
2. 关于source map,在生产环境下，一定要关闭source map选项，这样在chrome的调试窗口source选项卡下，就不会看到项目源码。否则，在build的dist/js目录下有map文件。
3. 使用vue cli使用的配置文件为 vue.config.js
```
module.exports = {
  productionSourceMap: false
}

```

## VUE插件

### [VUEX](https://vuex.vuejs.org/zh/)

1. Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。Vuex 也集成到 Vue 的官方调试工具 devtools extension，提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能。
2. 参考[购物车项目](https://github.com/vuejs/vuex/tree/dev/examples/shopping-cart)
3. store对象中的key值按照约定，不可随意更改。
4. 参考[vuex存储和本地存储(localstorage、sessionstorage)的区别](https://www.cnblogs.com/jsanntq/p/9288144.html).

#### cookie(HTML4) 

1. 4K

#### WebStorage(HTML5)

1. 5M或更大；
1. 使用方法

```
.运算符
[]运算符
setItem (key, value) ——  保存数据，以键值对的方式储存信息。
getItem (key) ——  获取数据，将键值传入，即可获取到对应的value值。
removeItem (key) ——  删除单个数据，根据键值移除对应的信息。
clear () ——  删除所有的数据
key (index) —— 获取某个索引的key
```

##### sessionStorage(HTML5) 回话存储

1. sessionStorage 方法针对一个 session 进行数据存储。当用户关闭浏览器窗口后，数据会被删除。

##### localStorage(HTML5) 本地存储

1. localStorage 方法存储的数据没有时间限制。第二天、第二周或下一年之后，数据依然可用。
 
#### 总结
 
1.区别：vuex存储在内存，localstorage（本地存储）则以文件的方式存储在本地,永久保存；sessionstorage( 会话存储 ) ,临时保存。localStorage和sessionStorage只能存储字符串类型，对于复杂的对象可以使用ECMAScript提供的JSON对象的stringify和parse来处理
2.应用场景：vuex用于组件之间的传值，localstorage，sessionstorage则主要用于不同页面之间的传值。
3.永久性：当刷新页面（这里的刷新页面指的是 --> F5刷新,属于清除内存了）时vuex存储的值会丢失，sessionstorage页面关闭后就清除掉了，localstorage不会。  
**注：很多同学觉得用localstorage可以代替vuex, 对于不变的数据确实可以，但是当两个组件共用一个数据源（对象或数组）时，如果其中一个组件改变了该数据源，希望另一个组件响应该变化时，localstorage，sessionstorage无法做到，原因就是区别1。**

### VUE-ROUTER

1. [vue router带参数页面刷新或者回退参数消失的解决方法](https://blog.csdn.net/bluefish_flying/article/details/81011230)
```
this.$router.push({path:'',params:{},query:{}})
patams传参.路径不能使用path 只能使用name,不然获取不到传的数据.query要用path
```
### [vue-json-excel](https://www.npmjs.com/package/vue-json-excel)


## 基于VUE的扩展

### [IVIEW](https://iviewui.com/)一套基于Vue.js的高质量UI 组件库

组件多于element-ui

### [Element](https://element.eleme.cn/#/zh-CN)一套为开发者、设计师和产品经理准备的基于 Vue 2.0 的桌面端组件库

1. [vue-element-admin](https://panjiachen.github.io/vue-element-admin-site/zh/)基于 Vue2.0，配合使用 Element UI 组件库的一个前端管理后台集成解决方案。

### [Ant Design of Vue](https://vue.ant.design/docs/vue/introduce/)

### [vue-beauty](https://fe-driver.github.io/vue-beauty/#/components/start)基于 vue.js 和 ant-design样式 的PC端 UI 组件库

### [ANTUE](https://zzuu666.github.io/antue/#/docs/development/first/zh)

### [fengyuanchen/vue-barcode](https://github.com/fengyuanchen/vue-barcode)



## 基于VUE的框架

### [vue-element-admin](https://panjiachen.github.io/vue-element-admin-site/zh/guide/#%E5%8A%9F%E8%83%BD)

