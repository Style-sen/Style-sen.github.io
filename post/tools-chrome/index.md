# chrome插件


[插件网站](https://www.crx4chrome.com/)

## 1. [t9tio/tomato-pie](https://github.com/t9tio/tomato-pie)    不太好用

## 2. [Awesome Screenshot](https://www.crx4chrome.com/extensions/nlipoenfbbikpbjkfpfillcgkoblgpmj/)    比较好用

## 3. Tampermonkey

### 3.1. 油猴脚本网站[Greasy Fork](https://greasyfork.org/zh-CN)

### 3.2. 编写油猴脚本

#### 3.2.1. 元数据块

元数据块是描述脚本的一个用户脚本部分。它通常包含脚本名称，命名空间，描述和包含和排除规则。元数据块出现在JavaScript行注释中，可能会出现在脚本内的任何位置， 但通常靠近文件的顶部

| 键                                  | 示例                                                                 | 备注                                                                                                                                                                 |
| ----------------------------------- | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| @name                               | @name  脚本名称                                                      | 脚本的名称。该项将显示在页面的标题以及链接内容，必填项                                                                                                               |
| @description                        | @description  脚本功能描述                                           | 脚本功能的描述，显示在脚本标题下面，必填项                                                                                                                           |
| @namespace                          |                                                                      | @namespace 及 @name 这两个属性将帮助用户脚本管理器判断是否已安装该脚本                                                                                               |
| @version                            | @version  0.0.1                                                      | 脚本的版本标记将使用 Mozilla 版本格式 并显示于脚本的简介页面，必填项                                                                                                 |
| @include @exclude @match            | @match  *://www.52pojie.cn/*                                         | 描述脚本将执行的页面。该列表会被分析并展示到脚本的简介页面，以及用于脚本分类                                                                                         |
| @require                            | @require http://cdn.bootcss.com/jquery.min.js                        | 引用外部脚本到您的脚本                                                                                                                                               |
| @updateURL @installURL @downloadURL |                                                                      | 告知用户脚本管理器应该在哪个地址获取脚本更新。                                                                                                                       |
| @license                            |                                                                      | 脚本所使用的许可协议名称或地址，该协议需包含用户是否允许二次分发或修改脚本的权利。不提供许可协议则表示用户仅允许个人使用且不得二次分发；该协议将在脚本的简介页面显示 |
| @supportURL                         |                                                                      | 用户可获得该脚本技术支持的链接地址 (如：错误反馈系统、论坛、电子  邮件)，该链接将显示在脚本的反馈页面                                                                |
| @contributionURL                    |                                                                      | 用于捐赠脚本作者的链接，该链接将显示在脚本的反馈页面                                                                                                                 |
| @contributionAmount                 |                                                                      | 建议捐赠金额，请配合 @contributionURL 使用                                                                                                                           |
| @compatible                         |                                                                      | 标记此脚本与某个浏览器兼容，兼容性信息将显示在脚本的简介页面上                                                                                                       |
| @incompatible                       |                                                                      | 标记此脚本与某个浏览器不兼容，兼容性信息将显示在脚本的简介页面上                                                                                                     |
| @run-at                             | document-start\document-body\document-end\document-idle\context-menu |


参考[Tampermonkey中文文档（部分）](https://blog.csdn.net/abc45628/article/details/53919135)

