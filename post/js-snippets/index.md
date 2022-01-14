# JS-snippets


## [使用JS现在文件](https://www.hacksparrow.com/using-node-js-to-download-files.html)

## [cunzaizhuyi/maskPlugin](https://github.com/cunzaizhuyi/maskPlugin)

1. 为一个视频添加一个覆盖物，从而挡住视频某区域，在视频的某一时间段，比如第10到第20分钟不显示划定的这块区域。应用场景包括遮挡卫视图标、遮挡视频右下角广告、充当马赛克等。
2. `有基于div和基于canvas两种技术方案，本文是使用canvas完成的`。
3. 主要原理是在HTML的video标签上贴上一个透明的canvas图层，然后响应mousedown、mousemove、mouseup事件。 由于canvas事件只是基于canvas元素，所以canvas内部每个元素（一个矩形，一个圆等）的事件响应要利用坐标来自己代码完成。

## [h5中利用canvas绘制video 忽略浏览器自带视频播放控件](https://www.jianshu.com/p/e5dfc486ecc6)

1. 使用canvas绘制视频。
2. 可以在视频中绘制其他按钮。
