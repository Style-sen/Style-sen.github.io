# QTCreater


1. QtCreater是插件设计架构，由插件管理器（负责插件的加载,管理,销毁等工作）和一堆插件（有一个叫core的插件,是QtCreator最基础的插件,提供了向界面增加菜单等功能）组成。
2. QtCreator的核心系统是由PluginManager和Core插件构成.前者负责插件的管理工作,后者负责提供QtCreator的最小功能集合,在PluginManager面前Core是当做普通插件进行加载,在自定义插件面前Core就是一个基础功能库,使用该库可以扩展QtCreator的功能.
3. QtCreator的所有功能,全是由插件实现,这种思路的优点是简化了顶层业务,也就是插件管理工作的逻辑,在那里只有PlunginManager和Plugin,缺点是增加了加载插件的复杂度,因为基础库这个插件需要被其他插件依赖,所以creator在插件加载时就必须要考虑插件之间的【依赖性】。

## 插件

1. 插件都需要继承IPlugin的接口，插件是由描述文件和继承IPlugin的类库组成。
2. 主要功能

```
A、在一个类中实现ExtensionSystem::IPlugin接口。
B、使用Q_EXPORT_PLUGIN宏导出插件类。
C、提供一个pluginspec插件描述文件，用于描述插件的元信息。
D、向其它插件暴露一个或多个对象。
E、查找其它插件暴露出来的可用的一个或多个对象。
```

2. 主要接口

```
//初始化函数,在插件被加载时会调用 
bool IPlugin::initialize(const QStringList &arguments, QString *errorString) 
//在所有插件的initialize函数被调用后,调用该函数,此时该插件依赖的插件已经初始化完成 
void IPlugin::extensionsInitialized()
//在所有插件的extensionsInitialized函数调用完成以后进行调用 
bool IPlugin::delayedInitialize()
```

### 暴露对象

1. 暴露对象是存在于插件管理器对象池中的对象。
2. 插件暴露出的对象会加入到PluginManager的对象池。PluginManager的allObjects()函数用于获取对象池中所有QObject对象的指针列表。
3. 一个对外暴露的对象是由一个插件对外暴露的QObject（或其子类）的实例，暴露对象存在于对象池中，并且可供其它插件使用。

## 契约

### 管理插件

1. 从main函数开始看,能够找到下面这个函数，PluginManager::loadPlugins()此函数为插件加载的机制。

### 使用核心系统的功能

1. 使用了核心系统的交互是在获取主编辑区那里,剩余就是插件内部的处理逻辑了


## 扩展

1. 扩展一下,在消息中间件,微服务盛行的今天,核心系统和插件完全可以设计成不在一个进程当中,我们可以把核心系统和插件之间通过远程调用的方式进行联系,核心系统与插件均可设计为单个进程或者服务,让整个系统的部署更加灵活,单个插件的问题也不会影响到整个系统.当然,核心系统与插件之间的发现使用机制,是需要我们结合实际使用场景进一步深入思考的.

本文来源：码农网
本文链接：https://www.codercto.com/a/39708.html

[QtCreator插件开发（三）——QtCreator架构](https://blog.csdn.net/u014357799/article/details/109853619)
