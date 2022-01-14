# QT基本模块-GUI


## 1. QBrush设置QPainter绘制的图形的填充图案

## 2. QColor颜色

```c++

```

## 3. QGuiApplication管理 GUI 应用程序的控制流和主要设置

```c++
//类常量
primaryScreen//默认屏幕
```

## 4. QMovie

1. 播放。

## 5. QScreen获取屏幕属性

```c++
//类常量
geometry//获取屏幕尺寸
```

## 6. QStandardItem

```c++
setForeground(const QBrush &brush)//设置前景
```

## 7. QStandardItemModel通用model

1. 继承自`QAbstractItemModel`;

```c++
//实例方法
item(int row, int column = 0) const //返回指定行和列的item
setHorizontalHeaderLabels(const QStringList &labels)    //使用labels设置水平标题标签。 如有必要，列数增加到labels的数量
rowCount(const QModelIndex &parent = QModelIndex()) const
```

