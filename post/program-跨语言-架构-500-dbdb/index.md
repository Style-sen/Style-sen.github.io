# PROGRAM 跨语言 架构 500 DBDB


## 代码结构

1. tool.py：命令行工具，从终端窗口来操作数据库
2. interface.py：使用二叉树实现方式定义了DBDB类->实现Python的字典接口。因此才能在Python程序中使用DBDB
3. logical.py：逻辑层，键值存储的抽象接口

    ```markdown
    LogicalBase：逻辑层更新的接口（get、set、commit）
    ValueRef：指向存储在数据库中的二进制数据的Python类（间接引用->避免一次性全将数据存到内存中）
    ```

4. binary_tree.py：逻辑接口之下的实体二叉树算法

    ```markdown
    BinaryTree：实现二叉树（get、insert、delete、key/value对），一成不变的 -- > 更新 -->返回一新的树。
    BinaryNode：二叉树的一个点
    BinaryNodeRef：实例化的ValueRef，可以 serialise and deserialise（连载和丢弃）一二叉树节点（BinaryNode）
    ```

5. physical.py：实体层，Storage类提供持久性（大多是只可加的）

## 总结

1. 【尽量不要使用类库】这样可以增加可移植性。
2. 先考虑逻辑，然后抽象接口；
3. 将大的功能模块拆分成小模块。

