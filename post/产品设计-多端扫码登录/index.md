# 产品设计-多端扫码设计


参考[产品经理小技术（三）：二维码这把利刃，产品应该用到极致](https://www.jianshu.com/p/60f33497c8c7)

# 第三方授权登录

参考[微信的网页版扫码登陆原理](https://www.zhihu.com/question/22991085)

# 网页端扫描二维码登录

1. 二维码是有失效时间以及失效状态的，一旦你扫过一次二维码或者在某段时间内没有扫描页面上的二维码，该二维码就失效了。

## 客户端

1. 获取一个有效的ID来生成二维码，并设置有效时间。超时需要重新生成。
2. 使用`长轮询`（25s超时）请求服务器获取ID的`绑定状态`及`登录状态`。
3. 只有登录状态才调到登录后的页面。

## 服务端

1. 维护ID与用户的绑定状态；
2. 支持长轮询。
3. 支持退出登录，即绑定状态失效。


