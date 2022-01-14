# QT基本模块-WIDGETS


## 1. 设计要点

`qmake:	QT += widgets`

1. 如果希望界面自适应电脑的分辨率或者改变界面大小时，界面上的所有部件也能够做出相应改变。这种时候就可以使用“布局（Layout）功能”。所谓布局，就是界面上组件的排列方式，使用布局可以使组件有规则地分布，并且随着窗体大小自动地调整大小和相对位置。
2. 需要为窗体制定一个总的布局。这样布局之后，当窗体大小改变时，各个组件都会自动改变大小。
3. 如果想要界面自适应电脑分辨率大小，可以将所有组件的`sizePolicy`中的`Horizontal Policy`和`Vertical Policy`设置成`Ignored`，再进行上述布局。
4. 【对于所有有区域的控件不要用绝对长度和宽度，全都用相对比例】。

### 1.1. 组件

#### 1.1.1. Window和Dialog

1. 没有嵌入父widget的widget称为window；
2. 通常，一个window有一个frame和一个title bar；也可以没有。
3. 任何没有父widget的QWidget都将成为一个window，并且在大多数平台上都将在桌面的任务栏中列出。这通常只适用于应用程序中的一个窗口，即`primary window`。
4. 另外，一个有父类的QWidget可以通过设置`Qt:: window`标志成为window。根据窗口管理系统的不同，这些`secondary windows`通常被堆叠在它们各自的父窗口的顶部，并且没有它们自己的任务栏条目。
5. `QMainWindow`类在其构造函数中设置了`Qt::Window`标志，因为它被设计成一个window，并提供了子窗口部件不需要的工具。
6. `Dialog Windows`用作向用户提供选项和选择的`secondary windows`。对话框是通过继承`QDialog`并使用小部件和布局来实现用户界面来创建的。

#### 1.1.2. 应用主窗口

##### 1.1.2.1. QMainWindow(类)

1. Qt用QMainWindow和相关类来管理`primary window`。QMainWindow已经定义了一个布局，可以往里添加一些 QToolBar 和 QDockWidget，也可以添加一个 QMenuBar 和一个 QStatusBar。这个布局有一个中央区域，可以放任意部件。
2. 继承自`QWidget`

```c++
menuBar()//返回主窗口的菜单栏QMenuBar，没有就会创建
```

#### 1.1.3. Dialog Windows现成的对话框

##### 1.1.3.1. QColorDialog(类)选择颜色

##### 1.1.3.2. QFileDialog(类)选择文件或者文件夹

##### 1.1.3.3. QFontDialog(类)选择字体

##### 1.1.3.4. QInputDialog(类)单值输入

##### 1.1.3.5. QMessageBox(类)弹出消息

1. 继承自`QDialog`;

```c++
//静态方法
question(QWidget *parent, const QString &title, const QString &text, QMessageBox::StandardButtons buttons = StandardButtons(Yes | No), QMessageBox::StandardButton defaultButton = NoButton)
QMessageBox::StandardButton warning(QWidget *parent, const QString &title, const QString &text, QMessageBox::StandardButtons buttons = Ok, QMessageBox::StandardButton defaultButton = NoButton)
```

##### 1.1.3.6. QProgressDialog(类)进度条

### 1.2. 样式

### 1.3. 布局

### 1.4. Model/View架构

### 1.5. Graphics View Framework

1. 图形视图框架用于管理和与大量定制的`2D`图形项进行交互，以及用于可视化这些项的视图小部件，支持缩放和旋转。

## 2. QAbstractButton

```c++
【信号】
void clicked(bool checked = false)//单击
```

## 3. QAbstractItemDelegate显示和编辑模型中的数据项

1. 继承自`QObject`.

```c++
//需要被继承类重写的方法
createEditor(QWidget *parent, const QStyleOptionViewItem &option, const QModelIndex &index)//创建一个控件用于编辑model中的数据，并返回。index为列号，option为显示选项
setModelData(QWidget *editor, QAbstractItemModel *model, const QModelIndex &index) //用编辑器的当前值设置model的值
setEditorData(QWidget *editor, const QModelIndex &index)//用model的值设置editor的值
```

## 4. QAbstractItemView抽象类

1. `QAbstractItemView`是所有的使用`QAbstractItemModel`模型的视图的基类，是一个不能被实例化的抽象类。它通过信号槽机制为与模型的交互操作提供了一个标准化的接口，确保子类视图能够随着模型的变化而及时更新。该类对键盘和鼠标的导航、视窗的滚动、项的编辑以及选择提供了标准的支持。

```c++
//实例方法
setItemDelegate(QAbstractItemDelegate *delegate)    //设置委托model
setItemDelegateForColumn(int column, QAbstractItemDelegate *delegate)//为column设置委托model
setItemDelegateForRow(int row, QAbstractItemDelegate *delegate) //设置委托model为row
setModel(QAbstractItemModel *model) //设置模型，view的操作就委托给了model进行处理，model变化view可以实时更新
selectionModel()//Returns the current selection model
```

## 5. QAction组件中的一个动作

## 6. QApplication

## 7. QBoxLayout水平或竖直排列子部件

## 8. QButtonGroup按钮组

1. 提供一个容器管理一组按钮。
2. 继承`QObject`.

```c++
QButtonGroup(QObject *parent = nullptr)//构造函数
//实例方法
addButton(QAbstractButton *button, int id = -1)//如果id为-1将自动给分配一个ID从-2开始减，如果指定id的话,请指定正数
button(int id) const//获取指定id的button
//信号
buttonClicked(QAbstractButton *button)//按键点击了一下
buttonPressed(QAbstractButton *button)
buttonReleased(QAbstractButton *button)
buttonToggled(QAbstractButton *button, bool checked)//checked is true if the button is checked, or false if the button is unchecked
idClicked(int id)
idPressed(int id)
idReleased(int id)
idToggled(int id, bool checked)//checked is true if the button is checked, or false if the button is unchecked.
```

## 9. QComboBox下拉选择框(单选)

```c++
addItem(const QString &text, const QVariant &userData = QVariant())//增加选项，可以自定义数据
addItem(const QIcon &icon, const QString &text, const QVariant &userData = QVariant())
addItems(const QStringList &texts)
clear()//清空所有选项
setCurrentIndex(index)//知道索引，就用
setCurrentText("text")//知道文本，就用
QVariant currentData(int role = Qt::UserRole) const
currentIndex() const
currentText() const
itemData(int index, int role = Qt::UserRole)//返回自定义数据
itemText(int index)//返回文本
model()//返回使用的model
findText(const QString &text, Qt::MatchFlags flags = Qt::MatchExactly|Qt::MatchCaseSensitive)//返回TEXT对应的index,没有找到返回-1
//【支持输入筛选】
setEditable(bool editable)//设置用户是否可以编辑，默认是false
setCompleter(QCompleter *completer)//只有可编辑才支持设置，不然会被忽略。注意输入之后需要判断是否在选项中
//【信号】
currentIndexChanged//当前索引改变触发
```

## 10. QCompleter

## 11. QDesktopWidget获取屏幕信息（该类已过时）

```c++
//实例方法
availableGeometry()//获取应用程序可用区域
```

## 12. QDialog

1. 对话框。
2. `this->setAttribute(Qt::WA_DeleteOnClose,true); // false 则不立即清空`,点击关闭立即杀死自己。
3. `setModal(true)`设置为模态。

## 13. QDialogButtonBox

```c++
//信号
accepted()
rejected()
```

## 14. QDockWidget

1. `QDockWidget`是可以在`QMainWindow`窗口停靠，或在桌面最上层浮动的window。

## 15. QFormLayout窗体布局

1. 与网格状布局类似，但是只有最右侧的一列网格会改变大小。

## 16. QFrame边框

1. 继承`QWidget`;

## 17. QGridLayout网格状布局

1. 网状布局大小改变时，每个网格的大小都改变。

## 18. QGroupBox带有标题的分组框

## 19. QHBoxLayout水平布局

1. 继承`QBoxLayout`;

```c++
QHBoxLayout(QWidget *parent)//构造函数
QHBoxLayout()//默认构造函数
```

## 20. QItemDelegate模型的显示和编辑功能

1. 继承`QAbstractItemDelegate`;
2. 一般被继承,委托。

```c++
//重写的公共成员函数

```

## 21. QListView提供列表或图标视图

1. 继承自`QAbstractItemView`;

## 22. QMenu(类)菜单

1. 用于菜单栏、上下文菜单栏和其他弹出菜单。

## 23. QMenuBar(类)水平菜单栏

1. 用来放置QMenu。

## 24. QPushButton

```c++
setMenu()//将一个按钮变为菜单按钮,点击下拉
```

## 25. QRadioButton单选按钮

1. 继承`QAbstractButton`；
2. 一般和`QButtonGroup`类配合使用。

```c++
QRadioButton(const QString &text, QWidget *parent = nullptr)//构造函数
```

## 26. QScrollArea

## 27. QSizePolicy描述水平和垂直大小调整策略的布局属性

```c++
//类型
enum QSizePolicy::Policy//【Fixed】不能放大或者缩小【Preferred】控件的sizeHint是它的sizeHint，但是可以放大或者缩小【Expanding】控件可以自行增大或者缩小
```

## 28. QSplitter分割器

## 29. QStatusBar水平状态栏

1. QStatusBar 可以添加多个 QWidget，因此，可以构建出很复杂的状态栏。

## 30. QStyle【抽象基类】封装了GUI的外观

```c++
【类型】
enum QStyle::StandardPixmap//标准位图图标
【实例方法】
standardIcon()//获取内置的标准图标
```

## 31. QTableView（表视图）

1. 继承自`QAbstractItemView`;
2. 适合大型项目。

## 32. QTableWidget表格组件

1. 继承自`QTableView`;
2. QTableWidget 约等于 QTableView + Model。
3. 适合小型项目。

```c++
//实例方法
setRowCount(int)//设置行数，增加减少行
setColumnCount(int)//设置列数
setHorizontalHeaderLabels(const QStringList)//设置表头标题
setHorizontalHeaderItem(int, QTableWidgetItem）//设置表头标题，适合对表头格式有要求的
clearContents()//只清除工作区，不清除表头
rowCount()//获取行数
stItem()//设置单元格，【必须先设置行数】
setCellWidget()//在单元格中设置控件
cellWidget()//获取单元格的控件
currentItem()//获取当前的item
item(int row, int column) const//获取指定行和列的item
//信号
itemChanged()//单元格的内容改变（enter，离开焦点）触发，背景颜色改变也会触发
```

## 33. QTableWidgetItem

1. 每个单元格（包括表头）。

```c++
//构造函数可以传入type，可以通过type()获取
setTextAlignment(int alignment)//设置文字对齐方式。
setBackground(const QBrush &brush)//设置单元格背景颜色。
setForeground(const QBrush &brush)//设置单元格前景色。
setIcon(const QIcon &icon)//为单元格设置一个图标。
setFont(const QFont &font)//为单元格显示文字设置字体。
setCheckState(Qt::CheckState state)//设置单元格勾选状态，单元格里出现一个QCheckBox组件。
setFlags(Qt::ItemFlags flags)//设置单元格的一些属性标记。
setFlags(Qt::ItemIsEditable)//单元格内容变为灰色，且不能选中单元格；用户不能编辑单元格内容，但是可以通过代码修改
setFlags(pItem->flags() & (~Qt::ItemIsEditable))//使单元格内容不会变为灰色，可以选中单元格，但是用户不能编辑单元格内容
```

## 34. QTabWidget选项卡

## 35. QTextBrowser

## 36. QTreeView提供了树视图的默认模型/视图实现

1. 继承自`QAbstractItemView`;

```c++
//实例方法
setWordWrap()//设置换行,并不会换行，只会增加省略号
```

## 37. QTreeWidget树形控件

1. 继承自`QTreeView`.

```c
//实例方法：
clear() //清空
topLevelItemCount()//获取顶层节点的数目
setHeaderHidden(true)//设置表头为隐藏
currentItem()//获取当前节点
//信号
itemClicked(QTreeWidgetItem *item, int column)//点击一个节点发出
```

## 38. QTreeWidgetItem 树形控件的节点

```C
QTreeWidgetItem(int type = Type)//在构造函数里传递一个类型值之后，就可以用 type() 返回这个节点的类型值
//实例方法
addChild(QTreeWidgetItem *child)//增加子节点
setData(int column , int role , const QVariant & value)//额外增加属性，不显示在UI中：column（列）role（enum Qt:: ItemDataRole）
QVariant data(int column , int role)//获取上边的额外属性
text(int column) //获取节点的文字
```

## 39. QScrollArea

1. 滚动区控件。

## 40. QVBoxLayout垂直方向布局

1. 继承`QBoxLayout`;

## 41. `QWidget`所有UI对象的基类

1. 一般和布局配合使用。

```c++
//static属性
//static方法
//实例属性
//实例方法
resize()//重新调整大小
QWidget *parentWidget() const//获取此组件的父组件
QString toolTip() const//控件的toolTip用来给控件进行注释等信息
//public slots
hide()//隐藏，不可见
//信号
void customContextMenuRequested(const QPoint &pos)//右键点击，上下文菜单
```

