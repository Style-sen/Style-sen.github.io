# 数据科学-机器学习


## 吴恩达的课程

1. [网易云课堂](https://study.163.com/note/noteIndex.htm?id=1004570029&type=0)
2. [fengdu78/Coursera-ML-AndrewNg-Notes](https://github.com/fengdu78/Coursera-ML-AndrewNg-Notes)
3. [Machine Learning Yearning 中文版](https://deeplearning-ai.github.io/machine-learning-yearning-cn/)

### 初识 
#### 什么是机器学习

1. 机器学习最主要的两类是监督学习和无监督学习，也是常用到的。
2. 监督学习就是我们会教计算机做某件事情；无监督学习中，我们让计算机自己学习。
3. 其他：强化学习；推荐系统。

#### 监督学习

1. 是指我们给算法一个数据集，其中包含了正确答案。算法的目的是给出更多的正确答案。
2. 也被称为回归问题，回归是指我们设法预测连续值的属性（房价）。
3. 分类问题（1或者0或更多离散值）（肿瘤），是指我们设法预测一个离散值的属性。 
4. 一个算法可以支持无穷多个特征。

#### 无监督学习

1. 给算法的数据集没有任何标签或有着相同的标签，让其在其中找到某种结构。
2. 聚类算法等（将数据集分成几簇）：谷歌新闻（抓取新闻分成新闻专题）、分局基因分组、管理大型数据中心、社交网络的分析（圈子和人脉）、市场细分、天文数据分析。
3. 鸡尾酒会问题 （多人语音分离，语音和背景音乐分离）；
4. 推荐使用[Octave](https://www.gnu.org/software/octave/)验证算法，然后再编写其他语言版。

### 单变量线性回归

#### 模型描述

1. m 表示训练样本的数量；x 表示输入变量或者特征；y 标识输出变量或目标变量。
2. （x,y）表示一个训练样本；（xi,yi）表示第i个训练样本。
3. 算法输出一个假设函数h（hypothesis）。

#### 代价函数

1. 模型参数。
2. 求线性函数的参数值，尽量使（预测的值和真实值的差的平方）的和最小。
3. [代价函数](https://blog.csdn.net/sd9110110/article/details/52863390)最常用的是平方误差代价函数.

#### 代价函数（一）

1. 代价函数可视化。令一个参数为0.

#### 代价函数（二）

1. 代价函数可视化。使用两个参数，使用等高线图。

#### 梯度下降

1. 环顾四周，向下降最快的方向迈出一步。
2. 可能得到多个局部最优。
3. 梯度下降算法的定义。
4. 参数需要同步更新。
5. [深入浅出--梯度下降法及其实现](https://www.jianshu.com/p/c7e642877b0e).

#### 梯度下降知识点总结

1. 合适的阿尔法值，太大有可能会导致发散。
2. 越接近最低点时，梯度下降法会自动选择更小的梯度。
3. 本视频使用一维变量。

#### 线性回归的梯度下降

1. 线性回归的代价函数总是一个弓状函数（凸convex函数），这个函数没有局部最优解。
2. 通常会从参数都是0点开始梯度下降。
3. batch梯度下降，每一步都遍历了整个训练集的样本。
4. 还有其他梯度下降算法，不用每次都遍历整个训练集，而是只关注一个小子集。
5. 求代价函数最小值的其他方法，正规方程组方法。梯度下降，适合比较大的数据集。

### 线性代数回顾

#### 矩阵和向量

1. 矩阵是由数字组成的矩形阵列，并卸载方括号中。
2. 矩阵的维数：行数X列数。
3. Aij（i行 j列）。
4. 向量是只有1列的矩阵。4维的向量即是含有四个元素的向量。
5. yi， 下标从1开始（表述），下标从0开始。

#### 加法和标量乘法

1. 只有相同维数的矩阵可以求和。将对应的每一个元素逐个求和。
2. 标量乘以一个矩阵，将这个标量和每一个元素相乘。

#### 矩阵向量乘法

1. 矩阵（mxn）乘向量（n）（矩阵的列数等于向量的行数）得到一个m维的向量：将矩阵的每一行和向量的列对应元素相乘求和，得到m维向量的对应行的元素。
2. 矩阵和向量相乘，可以求解线性代数。h(x)=-40+0.25x
3. 根据一个假设求房子（不同面积）的价格。

#### 矩阵乘法

1. 矩阵（mXn）乘矩阵（nXj）得到一个（mXj）维度的矩阵。将第二个矩阵的每一列与第一个矩阵的每一行对应元素相乘并求和，放在对应行与对应列的交叉点上。
2. 第一个矩阵的列数必须等于第二个矩阵的行数。
3. 根据多个假设，求房子（不同面积）的价格。

#### 矩阵乘法特征

1. NO交换律。
2. YES结合律。
3. 单位矩阵（I）是对角线为1，其他值为0的方阵。
4. mXn的矩阵A：IxA=AxI=A。前后I为不同的单位矩阵，前者为mXm的单位矩阵，后者为nXn的单位矩阵。

#### 逆和转置

1. 可逆矩阵一定是方阵。AA-1=A-1A=I
2. 如果矩阵A是可逆的，其逆矩阵是唯一的。
3. A的逆矩阵的逆矩阵还是A。记作（A-1）-1=A。
4. 可逆矩阵A的转置矩阵AT也可逆，并且（AT）-1=（A-1）T (转置的逆等于逆的转置）
5. 若矩阵A可逆，则矩阵A满足消去律。AB=AC（或BA=CA），则B=C。
6. 两个可逆矩阵的乘积依然可逆。
7. 矩阵可逆当且仅当它是满秩矩阵。
8. 不可逆的矩阵为奇异矩阵或退化矩阵。
9. 将矩阵的行列互换得到的新矩阵称为转置矩阵，转置矩阵的行列式不变。

### 多变量线性回归

#### 多功能

1. 比如预测房价有多个变量。n表示特征值的数量。假设函数为多变量线性函数。
2. 训练数据集可以为一个mX（n+1）维的矩阵，第一列为1.假设函数中的参数为一个（n+1）维的向量，训练数据集乘以向量得到预测的房价。

#### 多元梯度下降法

1. 多元线性回归的代价函数。J
2. 梯度下降

#### 多元梯度下降法演练1-特征缩放

1. 特征缩放确保所有特征都处在一个相近的范围，这样梯度下降就可以更快的收敛。否则代价函数的等值线可能是狭长的，这样梯度下降会花费更长的时间并且会来回摆动(比较曲折)。
2. 特征缩放就是将每个特征的取值进行缩放，缩放到-1和1的范围附近，比如除以样本值的最大值。
3. 均值归一化mean normalization。
4. Standardization 又称为 Z-score normalization，量化后的特征将服从标准正态分布.μ ， δ  分别为对应特征  xi  的均值和标准差。量化后的特征将分布在  [−1,1]  区间。
5. Min-Max Scaling 又称为 normalization，量化后的特征将分布在  [0,1]  区间。
6. 大多数机器学习算法中，会选择 Standardization 来进行特征缩放，但是，Min-Max Scaling 也并非会被弃置一地。在数字图像处理中，像素强度通常就会被量化到  [0,1]  区间，在一般的神经网络算法中，也会要求特征被量化到  [0,1]  区间。
7. 进行了特征缩放以后，代价函数的轮廓会是“偏圆”的，梯度下降过程更加笔直，性能因此也得到提升.

#### 多元梯度下降法II-学习率

1. 绘出代价函数的值与迭代次数的曲线。可以告诉你大致在多少次之后收敛到最小值，并且判断梯度下降是否正常工作。
2. 通常以3倍的间隔选择合适的学习率，即步长。

参考[特征缩放](https://yoyoyohamapi.gitbooks.io/mit-ml/content/%E7%BA%BF%E6%80%A7%E5%9B%9E%E5%BD%92/articles/%E7%89%B9%E5%BE%81%E7%BC%A9%E6%94%BE.html)

#### 特征和多项式回归

1. 根据已有特征创造新的特征，可能会找到更好的算法模型。
2. 对于一个非线性的假设函数(二次或 三次 或平方根函数)，可以通过创建新的特征值转换为多元线性回归来解决问题。
3. 我们可以选择多种多样的特征值，到底该如何选择，后边会有 算法把我们选择合适的特征值。

#### 正规方程（区别于迭代方法的直接解法）

1. 正规方程提供了一次性求解代价函数参数最优解的解析解法。求解导数为零时的参数值。
2. 对于参数为一个向量，（X的转置乘以X）的逆乘以X的转置再乘以y得到参数的向量值。
3. `pinv(X'*X)*X'*y`伪逆
4. 优点：不需要学习率，不需要迭代。缺点：对于特征值很多（一般多于1万）的情况，还是选用梯度下降。
5. 正规方程不适用一些复杂的算法，比如逻辑回归 算法。此时需要梯度下降的算法。

#### 正规方程在矩阵不可逆的情况下的解决方法
1. （X的转置乘以X）不可逆的情况，其实很少发生。
2. 有两个原因：特征中包含了多余特征（比如有两个特征存在线性关系）。解决方法，删除一些特征。
3. 第二个原因，特征值的数目n大于样本的数目m。解决方法:删除一些特征或使用正则化的方法。

### Octave教程

见[数据科学-OCTAVE](https://style-sen.github.io/2019/06/20/%E6%95%B0%E6%8D%AE%E7%A7%91%E5%AD%A6-OCTAVE/)

#### 向量化

1. 重点是梯度下降的向量化，参考[关于梯度下降算法的矢量化过程](https://blog.csdn.net/the_lastest/article/details/72851635).
2. 理解要点在梯度。梯度矩阵的元素分别求值，利用分号创建矩阵。

### Logistic回归

#### 分类

1. 不能用线性回归处理分类问题，因为线性算法的输出值要么很大或很小，显然对于0和1不是很合适。
2. 逻辑回归的算法的值一直介于0和1之间，并不会大于1或 小于0.

#### 假设陈述

1. 当有一个分类问题的时候，我们要用哪个方程来表示我们的假设。
2. （对y进行归一化）logistics函数：$g(z)=\frac{1}{1+e^{-z}}$。
3. 逻辑回归本质上是线性回归，只是在特征到结果的映射中加入了一层函数映射，即先把特征线性求和，然后使用函数g(z)将最为假设函数来预测。g(z)可以将连续值映射到0到1之间。线性回归模型的表达式带入g(z)，就得到逻辑回归的表达式:$h_{\theta}(x)=g\left(\theta^{T} x\right)=\frac{1}{1+e^{-\theta^{T} x}}$。
4. 假设函数输出的y是x对应的分类问题的概率估计：$P(y=1 | x ; \theta)=h_{\theta}(x)$和$P(y=0 | x ; \theta)=1-h_{\theta}(x)$。

#### 决策界限

1. 决策边界是假设函数的属性。只要假设函数及其参数确定，此边界也确定。（首先会预测在什么范围下分类）
2. 我们可以用非常复杂的模型来适应非常复杂形状的判定边界。

#### 代价函数

1. 如果还是用线性回归时的代价函数，会发现此函数是非凸函数，有很多个局部最小值。我们需要其他形式的代价函数来保证逻辑回归的代价函数是凸函数。
2. 这里我们先对线性回归模型中的代价函数J(θ)进行简单的改写：$J(\theta)=\frac{1}{m} \sum_{i=1}^{m} \frac{1}{2}\left(h_{\theta}\left(x^{(i)}\right)-y^{(i)}\right)^{2}$；
3. 用Cost(h(x), y) = 1/2(h(x) - y)^2 代替：$J(\theta)=\frac{1}{m} \sum_{i=1}^{m} \operatorname{cost}\left(h_{\theta}\left(x^{(i)}\right), y^{(i)}\right)$；
4. 在这里我们选择对数似然损失函数做为逻辑回归模型的代价函数，Cost函数可以表示如下：$$
\operatorname{cost}\left(h_{\theta}(x), y\right)=\left\{\begin{aligned}-\log \left(h_{\theta}(x)\right) & \text { if } y=1 \\-\log \left(1-h_{\theta}(x)\right) & \text { if } y=0 \end{aligned}\right.
$$；
5. 为了统一表示，可以把Cost(h(x), y)表达成统一的式子，根据前面J(θ)的定义，J(θ)等于：
$J(\theta)=-\frac{1}{m}\left[\sum_{i=1}^{m} y^{(i)} \operatorname{logh}_{\theta}\left(x^{(i)}\right)+\left(1-y^{(i)}\right) \log \left(1-h_{\theta}\left(x^{(i)}\right)\right)\right]$
6. 特别说明: 1. 当y=1的时候，第二项(1-y)log(1-h(x))等于0 ；2. 当y=0的时候，ylog(h(x))等于0。
7. 根据线性回归求代价函数的方法，可以用梯度下降算法求解参数θ。

#### 简化代价函数与梯度下降

#### 高级优化

1.  梯度下降（Gradient descent）并不是我们可以使用的唯一算法。
2. 下面三种高级算法，通常不用手动选择学习率。他们有一个智能内循环，称为线搜索算法，自动尝试不同的学习率并选择一个好的学习率，甚至为每次迭代选择不同的学习率。还有其他优化，使代价函数收敛的速度远远大于梯度下降。
3. Conjugate Gradient
4. BFGS
5. L-BFGS
6. 上述三种算法的缺点是特别复杂，不要尝试自己实现。但是不同的语言实现他们是有差别的。

```
function [jVal,gradient] = costFunction(theta)
jVal = (theta(1)-5)^2+(theta(2)-5)^2;
gradient = zeros(2,1);
gradient(1) = 2*(theta(1)-5);
gradient(2) = 2*(theta(2)-5);
end

options = optimset('GradObj','on','MaxIter',100);

第一个参数为GradObj:on意为设置目标参数为打开，意为要给该算法设置一个梯度。
第二个参数为MaxLter:100意为最大迭代次数为100。

initialTheta = zeros(2,1);
[optTheta,functionVal,exitFlag] = fminunc(@costFunction,initialTheta,options);

Fminunc是一个高级算法，第一个参数为代价函数，无需设置学习效率，该算法会自动计算出相应的学习效率
```

#### 多元分类：一对多（one vs all）(one vs rest)

1. 多类别分类问题。
2. 对于每一类都有一个逻辑回归二分类预测函数。有多少类就会有多少个。
3. 然后对于新的值，套用所有的预测函数，那个函数得到的概率越高，就属于哪一类。

### 正则化

#### 过拟合问题

1. 欠拟合：高偏差；过拟合：高方差。
2. 过拟合：拟合训练集很好，无法泛化到新样本。
3. 怎么解决过拟合：(1)尽量减少选取变量的数量：可以人工检查变量清单，以确定那些特征变量需要保留，也可以使用模型选择算法，缺点是舍弃了一些有用的特征变量。（2）正则化：保留所有的特征变量，但是减少量级。

#### 代价函数

1. 给代价函数加入惩罚项，也就是正则项。这样可以弱化指定的参数对式子的影响。
2. 但是如果特征很多的情况下，我们无法确定应该惩罚哪些参数，这时就要惩罚所有参数，来缩小每一个参数。
3. 正则化参数，作用是平衡两个目标之间的取舍（更好地拟合训练集，将参数控制地更小保持模型的相对简单。）
4. 正则化参数设置过大，就会欠拟合；过小，可能过拟合。

#### 线性回归的正则化

1. 梯度下降加入正则化。
2. 正规方程加入正则化。

#### 逻辑回归的正则化

1. 梯度下降加入正则化。
2. 高级优化加入正则化。

### 神经网络学习

#### 非线性假设

1. 如果特征值非常大的情况下，使用逻辑回归，就不是很好的选择，需要考虑神经网络。

#### 神经元与大脑

1. 不同的传感器接到大脑上，大脑都会自动的处理传输过来的数据，即便使用的是同一块皮层。

#### 模型展示I

1. 输入层（x0偏置单元，为1，在输入层的神经元称为输入神经元）、隐藏层（允许有多层）、输出层（在输出层的神经元称为输出神经元）。
2. 激活函数（作用是将权值结果转化为分类结果）。

```
sigmoid函数

softmax函数

tanh函数：双曲正切


```

3. 参数（权重）
4. 计算步骤

## 《机器学习实战》

## 《机器学习》周志华

## 《统计学习方法》李航

## 实际工作

1. 大部分的工作只是洗数据、调参数。
2. 算法的原理。
