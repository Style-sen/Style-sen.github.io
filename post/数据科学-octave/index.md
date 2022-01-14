# 数据科学-OCTAVE


## INSTALL

### [download](https://www.gnu.org/software/octave/download.html)
### `./configure`

1. `configure: error: a Fortran compiler is required to build Octave`:`sudo apt-get install gfortran`
2. `configure: error: BLAS and LAPACK libraries are required`:`sudo apt-get install libblas-dev libatlas-base-dev liblapack-dev`

### `sudo make  install`

##  语法

### 基本操作

1. %为注释。
2. `PS1('>>')`更改提示符。
3. 在语句后加`;`可以阻止打印输出。

### 基本计算

1. 支持基本数学运算。
2. 支持判断运算符：==（等于）、~=（不相等）、&&（与）、||（或）。
3. `pwd`返回octave的安装路径。
4. `cd ''`改变路径。
5. `ls`列出文件。

### 内建函数

|函数|功能||
|--|--|--|
|xor|异或|
|sqrt|求根|
|cos|余弦函数（弧度制）|
|sin|正弦函数（弧度制）|
|tan|正切函数（弧度制）|
|exp|指数函数（$e^{x}$）|对矩阵每个元素求解|
|log|以e为底的指数函数|
|log10
|sinh
|tanh
|cosh
|acos
|acosh
|asin
|asinh
|atan
|atan2
|atanh
|abs||对矩阵中所有元素取绝对值|
|sign
|round
|floor
|ceil
|fix
|rem
|-V||对矩阵中所有元素取反|
|1/V||对矩阵中所有元素取导数|

### 变量

1. `a=3`定义一个变量，并赋值3.
2. `b='hello'`定义一个字符串变量。
3. `c=(3>=1)`定义一个bool类型的变量。
4. `disp(a)`显示变量a的值或直接`a`回车。
5. `disp(sprintf('2 decimals: %0.2f', a))`格式化输出。
6. `format long`显示默认的位数。
7. `format short`显示少量的位数。
8. `who`显示octave在内存中存储的所有变量。
9. `whos`显示octave在内存中存储的所有变量的更详细的信息。
10. `clear`删除所有变量。
11. `clear 变量名`删除指定变量。

### 向量和矩阵

1. `A=[1 2;3 4;5 6]`产生一个3行2列的矩阵A。
2. `v=[1 2 2]`定义一个行向量。
3. `v=1:0.1:2`定义一组数，从1开始，步长为0.1一直增加到2.1x11的矩阵。
4. `v=1:6`定义[1 2 3 4 5 6].
5. `ones(2,3)`生成2X3的矩阵，元素都为1.
6. `zeros(1,3)`生成1X3的矩阵，元素都为0；
7. `rand(3,3)`生成3X3的矩阵，元素为随机数（0~1之间）。
8. `randn(1,3)`服从高斯分布，均值为0.标准差或者方差为1.
9. `hist(w,50)`绘制一个矩阵的直方图，并指定柱的数目。
10. `eye(n)`生成n维的单位矩阵。
11. `size(A)`返回矩阵的大小（维数）即一个1X2的矩阵。
12. `size(A,1)`返回矩阵A第一维度的大小。
13. `length(A)`返回矩阵A较大的那个维数，通常对变量使用。
14. `v=priceY(1:10)`将priceY的前10个元素赋给v.
15. `A(3,2)`获得第3行第二列的元素。
16. `A(3,:)`获得第三行的全部元素。
17. `A([1,3],:)`获得第1行和第3行的全部元素。
18. `A(:,2)=[10;11;12]`将A的第2列元素替换。
19. `A=[A,[100;101;102]]`在A的右边追加一列元素。
20. `A(:)`将A中的所有元素放进一个列向量。
21. `c=[A B]`将A和B放进一个矩阵中。
22. `C=[A;B]`将B放在A的下方。

23. `A*C`A和C矩阵相乘。
24. `A.*B`将矩阵A和矩阵B的对应元素进行相乘。点号用来表示元素的运算。
25. `A'`求A的转置。
26. `max(A)`获得A中最大元素。
27. `[val, ind]=max(A)`val为A中最大的元素，ind为此值的索引。
28. `find(a<3>)`找到小于3的元素，返回他们的索引。
29. `magic(3)`返回幻方的方阵，他的任一行、列、对角线的和相同。
30. `[r,c]=find(A>=7)`r为大于等于7的行号，c为大于等于7的列号。
31. `sum(A)`求矩阵所有元素的和。`sum(A,1)`取每一列的和。`sum(A,2)`取每一行的和。
32. `prod(A)`求所有元素的积。
33. `floor(A)`所有元素向下取整。
34. `ceil(A`所有元素向上取整。
35. `max(rand(3),rand(3))`取两个随机矩阵中的大值组成新的矩阵。
36. `max(A,[],1)`取每一列的最大值 。
37. `max(A,[],2)`取每一行的最大值。
38. `flipud(A)`使矩阵A竖直反转。
39. `pinv(A)`求逆。

### 文件

1. `load xxxx.dat`和`load('xxxx.dat')`加载数据，并生成以文件名命名的变量。
2. `save hello.mat v`将变量v保存为hello.mat的文件（二进制）。
3. `save hello.txt v -ascii`将变量保存为用ascii编码的文档。


### 图形绘制 

1. `plot(x, y1)`绘制图形,会清除原有图形。`plot(x, y, x, h, '.-')`绘制多条线。
2. `hold on`不清楚原有图形，叠加绘图。
3. `xlabel('x')`添加 x 轴标签。
4. `ylabel('Sin(x)')`添加 y 轴标签。
5. `title('Sin(x) Graph')`添加标题。
6. `grid on`显示网格线。
7. `axis equal`使横轴和纵轴的间隔相等。
8. `legend('Sin(x)', 'Cos(x)')`在图形中添加图例。
9. `axis([xmin xmax ymin ymax])`设置xy轴的刻度。
10. `subplot(m, n, p)`创建子图。
11. 输入多条命令用逗号隔开。
12. `figure`给图形标号
13. `print -dpng ‘myPlot.png’`保存图形，保存图形到 ‘myPlot.png’ 文件中，保存目录为当前工作路径。
14. `close`关闭图形
15. `clf`清除图形.
16. `bar()`绘制条形图。
17. meshgrid 命令是用于产生一个矩阵的元素，赋予 x xx 和 y yy 的范围内进行了在每一种情况下的增量同规格一起。
```
[x, y] = meshgrid(-5: 0.1: 5, -3: 0.1: 3); %
g = x.^2 + y.^2;           % 函数 g
[C, h] = contour(x, y, g); % 绘制等高线图
set(h, 'ShowText', 'on', 'TextStep', get(h, 'LevelStep')*2);
```
18. `surf(x, y, g)`绘制三维图.
19. `imagesc(A)`将矩阵A中的元素数值按大小转化为不同颜色，并在坐标轴对应位置处以这种颜色染色imagesc(x,y,A) x,y决定坐标范围，x,y应是两个二维向量.
20. `colorbar`在上图中加入颜色指示条。`colormap gray`将颜色变为灰度。


### 控制语句

```octave
% FOR
for i=1:10,
    V(i) = 2^i;
end;
>> indices = 1:10;
>> for i=indices,
       disp(i);
   end;
>> while i <= 5,
        disp(V(i));
        i = i+1;
   end;
>> while true,
        disp(V(i));
        if i > 5,
            break;
        end;
        i = i + 1;
   end;
```

### 定义函数

1. 在Octave中，定义一个函数需要使用function 关键字，然后紧跟在 function 后面的是函数的声明，包括返回值，函数名称和参数，之后换行来实现具体的函数功能。
2. Octave的函数不需要显式的返回语句，Octave会将函数第一行声明的返回值返回给调用方，因此，我们在函数体中只需将最终的计算结果赋给定义的返回值。
```
>> function [y1, y2] = calVal(x)
       y1 = x^2;
       y2 = x^3;
   end;
```
3. 在该目录下新建一个文件名为“squareThisNumber.m”后缀是.m这样octave可以自动识别。（注意：文件名要和函数名保持一致）。在octave中直接调用就可以。
4. `addpath("")`添加函数搜索路径。
5. 代价函数。
```
>> X=[1 1;1 2;1 3;1 4]
>> Y=[1;2;3;4]
```

## 内置函数

### fminunc

自动的从众多高级优化算法中挑选一个来使用(你也可以把它当做一个可以自动选择合适的学习速率aa的梯度下降算法)。
