# SHELL


注：命令后台执行。

```shell
1. command & #后台运行，你关掉终端会停止运行
2. nohup command & #后台运行，你关掉终端也会继续运行
```

## 1. 变量

### 1.1. 定义变量

1. 定义：`your_name="runoob.com"`,变量名和等号之间不能有空格.
2. 用语句给变量赋值： for file in `ls /etc` 或 for file in $(ls /etc)。

### 1.2. 使用变量

```shell
echo $your_name
echo ${your_name}
```

### 1.3. 只读变量

```shell
readonly myUrl
```

### 1.4. 删除变量

```shell
unset variable_name # unset 命令不能删除只读变量
```

### 1.5. 变量类型

#### 1.5.1. shell变量

```shell
$$                # Shell本身的PID（ProcessID） 
$!                # Shell最后运行的后台Process的PID 
$?                # 最后运行的命令的结束代码（返回值） 终止状态。
$-                # 使用Set命令设定的Flag一览 
$*                # 所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。 
$@                # 所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。 
$#                # 添加到Shell的参数个数 
$0                # Shell本身的文件名,但在bash下，执行source,为'bash';
$1～$n            # 添加到Shell的各参数值。$1是第1参数、$2是第2参数…。
$BASH_SOURCE      # 【bash】取得当前执行的 shell 文件所在的路径及文件名
$BASH_SOURCE[0]   # 【bash】等价于 BASH_SOURCE ,
$SHELL            # 当前默认使用的shell
```

### 1.6. 字符串

1. 字符串是shell编程中最常用最有用的数据类型（除了数字和字符串，也没啥其它类型好用了），字符串可以用单引号，也可以用双引号，也可以不用引号。
2. 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
3. 双引号里可以有变量.
4. 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。
5. 双引号里可以出现转义字符

#### 1.6.1. 拼接

1. 使用双引号拼接`greeting_1="hello, ${your_name} !"`
2. 使用单引号拼接`greeting_2='hello, '$your_name' !'`

### 1.7. 数组

## 2. 运算符

### 2.1. 算数运算符

1. 默认情况下，Shell 不会直接进行算术运算，而是把+两边的数据（数值或者变量）当做字符串.
2. 要想让数学计算发挥作用，必须使用数学计算命令.
3. (()) 和 bc 即可.

| 运算操作符/运算命令 | 说明                                                                                                                                                                   | 示例 |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| (( ))               | 用于整数运算，效率很高，推荐使用。                                                                                                                                     |
| let                 | 用于整数运算，和 (()) 类似。                                                                                                                                           |
| $[]                 | 用于整数运算，不如 (()) 灵活。                                                                                                                                         |
| expr                | 可用于整数运算，也可以处理字符串。比较麻烦，需要注意各种细节，不推荐使用。                                                                                             |
| bc                  | Linux下的一个计算器程序，可以处理整数和小数。Shell 本身只支持整数运算，想计算小数就得使用 bc 这个外部的计算器。                                                        |
| declare -i          | 将变量定义为整数，然后再进行数学运算时就不会被当做字符串了。功能有限，仅支持最基本的数学运算（加减乘除和取余），不支持逻辑运算、自增自减等，所以在实际开发中很少使用。 |

#### 2.1.1. (())

```shell
((a=10+66)
((b=a-15))
((c=a+b))	这种写法可以在计算完成后给变量赋值。以 ((b=a-15)) 为例，即将 a-15 的运算结果赋值给变量 c。

注意，使用变量时不用加$前缀，(( )) 会自动解析变量名。
a=$((10+66)
b=$((a-15))
c=$((a+b))	可以在 (( )) 前面加上$符号获取 (( )) 命令的执行结果，也即获取整个表达式的值。以 c=$((a+b)) 为例，即将 a+b 这个表达式的运算结果赋值给变量 c。

注意，类似 c=((a+b)) 这样的写法是错误的，不加$就不能取得表达式的结果。
((a>7 && b==c))	(( )) 也可以进行逻辑运算，在 if 语句中常会使用逻辑运算。
echo $((a+10))	需要立即输出表达式的运算结果时，可以在 (( )) 前面加$符号。
((a=3+5, b=a+10))	对多个表达式同时进行计算。
```

### 2.2. 关系运算符

1. `关系运算符只支持数字，不支持字符串，除非字符串的值是数字`。

| 运算符 | 说明                                                  | 举例(a为10，b为20)         |
| ------ | ----------------------------------------------------- | -------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true。                   | [ $a -eq $b ] 返回 false。 |
| -ne    | 检测两个数是否不相等，不相等返回 true。               | [ $a -ne $b ] 返回 true。  |
| -gt    | 检测左边的数是否大于右边的，如果是，则返回 true。     | [ $a -gt $b ] 返回 false。 |
| -lt    | 检测左边的数是否小于右边的，如果是，则返回 true。     | [ $a -lt $b ] 返回 true。  |
| -ge    | 检测左边的数是否大于等于右边的，如果是，则返回 true。 | [ $a -ge $b ] 返回 false。 |
| -le    | 检测左边的数是否小于等于右边的，如果是，则返回 true。 | [ $a -le $b ] 返回 true。  |

### 2.3. 布尔运算符

### 2.4. 逻辑运算符

1. 【注】有逻辑运算符必须要双[].

| 运算符 | 说明       | 举例                                        |
| ------ | ---------- | ------------------------------------------- |
| `&&`   | 逻辑的 AND | `[[ $a -lt 100 && $b -gt 100 ]]` 返回 false |
| `||`   | 逻辑的 OR  | `[[ $a -lt 100 || $b -gt 100 ]]` 返回 true  |

### 2.5. 字符串运算符

1. 字符串比较直接用`=`.

### 2.6. 文件测试运算符

## 3. 流程控制

### 3.1. if

1. `【从本质上讲，if 检测的是命令的退出状态】`
2. expression 和方括号([ ])之间必须有空格，否则会有语法错误

```shell
if [ expression 1 ]
then
   Statement(s) to be executed if expression 1 is默认 true
elif [ expression 2 ]
then
   Statement(s) to be executed if expression 2 is true
elif [ expression 3 ]
then
   Statement(s) to be executed if expression 3 is true
else
   Statement(s) to be executed if no expression is true
fi
```

### 3.2. for

```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

`for var in item1 item2 ... itemN; do command1; command2… done;`

## 4. 注释

1. 【注】不要在行后加注释。

