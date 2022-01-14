# JS笔记


[CANVAS 使用实时凸包算法实现](http://wow.techbrood.com/fiddle/27882)    

## 规范

[JavaScript Standard Style](https://standardjs.com/readme-zhcn.html)

## 插件

### Velocity.js
1. Velocity.js是一款动画切换插件，它重新实现了jQuery的$.animate()方法，并且你无须引入JQuery。它比JQuery更快，并实现了变色动画，形变，循环，擦除和SVG支持以及滚动。它是jQuery和CSS变换 的最佳组合。
2. Velocity 接收一组 css 属性键值对 (css map) 作为它的第一个参数，该参数作为动画效果的最终属性。第二个参数是可选参数 为动画的额外配置项
```
// 当使用jQuery时，Velocity和jQuery的animate()用法类似
$("#test").velocity({
    left: "200px"
}, {
    duration: 450,
    delay: 300
});
```
3. 动画配置项

duration: 400,         // 动画执行时间
easing: "swing",       // 缓动效果
queue: "",             // 队列
begin: undefined,      // 动画开始时的回调函数
progress: undefined,   // 动画执行中的回调函数（该函数会随着动画执行被不断触发）
complete: undefined,   // 动画结束时的回调函数
display: undefined,    // 动画结束时设置元素的 css display 属性
visibility: undefined, // 动画结束时设置元素的 css visibility 属性
loop: false,           // 循环
delay: false,          // 延迟
mobileHA: true         // 移动端硬件加速（默认开启）

### Jquery

1. $方法：$就是jquery对象，$()就是jQuery()，在里面可以传参数，作用就是获取元素
```
$(".div1") 表示获取类名为div1的元素，例如获取<div class="div1"></div>
$(".div1").onclick表示类名为div1的div点击事件
jquery中$.，例如$.post()，$.get()，$.ajax()等这些都是jquery这个对象的方法
```

| 语句 | 功能 |
|--|--|
| $(document).ready(function(){}) | 必须等到页面内包括图片的所有元素加载完毕后才能执行 |
| $(window).ready() | DOM结构绘制完毕后就执行，不必等到加载完毕 |

2. [JQ $.ajax和$.getJson同步异步切换](https://blog.csdn.net/qq_16291159/article/details/76093459)

