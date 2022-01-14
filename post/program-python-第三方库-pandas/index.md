# pandas


## 概述

1. `缺点`不具备多处理器，处理较大的数据集速度很慢。

## 1. def 方法

## 2. class 类

1. 最重要的两个数据结构为Series和DataFrames。
2. 对齐数据和缺失数据（dropna()和fillna()）,层次化索引。

### 2.1. Series 类

1. 对numpy进行了封装。
2. 索引可以重复。

```python
## 定义
obj = Series([1,2,3,4,5],index=["a","b","c","d","e"])
"a" in obj # 判断索引
obj['a']  # 访问index
obj1 = Series(字典)
obj1.index = # 更改index
obj1.reindex([1，2，3，4],fill_value=0) # 重新定义index，空值填充0
obj1.reindex([1，2，3，4],method='ffill') # 重新定义index，利用上边的值填充，'bfill'利用后边的值填充
obj1.dropna() # 删除缺失值
## 层次化索引，index是一个数组

```

### 2.2. DataFrames 类

1. 一般说来dataframe就是a set of columns, each column is an array of values.
2. In pandas, the array is one way or another a (maybe variant of) numpy ndarray. 而ndarray本身不存在一种in place append的操作。因为它实际上是一段连续内存。任何需要改变ndarray长度的操作都涉及分配一段长度合适的新的内存，然后copy。【这是这类操作慢的原因】。
3. 如果pandas dataframe没有用其他设计减少copy的话，我相信Bren说的"That's probably as efficient as any"是很对的。所以in general, 正如Bren说的。Pandas/numpy structures are fundamentally not suited for efficiently growing.Matti 和arynaq说的是两种常见的对付这个问题的方法。我想Matti实际的意思是把要加的rows收集成起来然后concatenate, 这样只copy一次。arynaq的方法就是预先分配内存比较好理解。如果你真的需要incrementally build a dataframe的话，估计你需要实际测试一下两种方法。==我的建议是，如有可能，尽力避免incrementally build a dataframe, 比如用其他data structure 收集齐所有data然后转变成dataframe做分析==。
4. `df = pd.DataFrame(一个字典)`如果使用所有标量值（key和value也是标量）,value是一个值的列表，必须设置一个索引（index=字典.keys()）.
5. 可以使用`[]`和`.`访问一列的值。

#### 2.2.1. 实例属性

```python
df.dtypes  "每一列的类型"
df.index   ""
df.columns "列名"
   columns.tolist()
df.values  ""
df.T       "转置"
```

#### 2.2.2. 实例方法

1. 根据一列通过算法生成另一列

```python
# 使用apply函数, 如果city字段包含'ing'关键词，则'判断'这一列赋值为1,否则为0
如： 
frame['panduan'] = frame.city.apply(lambda x: 1 if 'ing' in x else 0)
frame['cap'] = frame.city == "北京"
```

2. DataFrame的排名

```python
rank(axis=0, method='average', numeric_only=None, na_option='keep', ascending=True, pct=False)
```

| 参数         | 取值                                        | 功能                                                                                                                                                                                                 |
| ------------ | ------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| axis         | {0 or ‘index’, 1 or ‘columns’}, default 0   | index to direct ranking                                                                                                                                                                              |
| method       | {‘average’, ‘min’, ‘max’, ‘first’, ‘dense’} | average:相同的值给一个平均排名，min: 对于相同的值都取小的排名，max: 对于相同的值都取大的排名，first:对于相同的值按照出现的顺序排名，dense: like ‘min’, but rank always increases by 1 between groups |
| numeric_only | boolean:default None                        | Include only float, int, boolean data. Valid only for DataFrame or Panel objects                                                                                                                     |
| na_option    | {‘keep’, ‘top’, ‘bottom’}                   | keep: leave NA values where they are,top: smallest rank if ascending,bottom: smallest rank if descending                                                                                             |
| ascending    | boolean, default True                       | False从高(1)到低(N)排序                                                                                                                                                                              |
| pct          | boolean, default False                      | Computes percentage rank of data                                                                                                                                                                     |

3. DataFrame索引

```python
DataFrame.set_index(keys, drop=True, append=False, inplace=False, verify_integrity=False)
```

4. DataFrame将一列导出数组

```python
df['column'].tolist()
```

```python
pd.date_range('20130101', periods=6)
pd.to_datetime(datetime对象)     #转化为日期类型
```

# 3. dataframe

```python
## 定义
df = pd.DataFrame(np.random.randn(6,4), index=dates, columns=list('ABCD'))
df2 = pd.DataFrame({'A' : 1.,
                    'B' : pd.Timestamp('20130102'),
                    'C' : pd.Series(1,index=list(range(4)),dtype='float32'),
                    'D' : np.array([3] * 4,dtype='int32'),
                    'E' : pd.Categorical(["test","train","test","train"]),
                    'F' : 'foo' })

## 实例属性

## 实例方法
df.head()  #前5行
df.tail()  ""
df.describe()  "显示快速统计"
df.iteritems()
df.iterrows()
df.itertuples()
df.count()    "计数"
df["列名"].sum()
df.astype(str)    "将dataframe所有内容变成字符串"
df[].str.contains()    "字符串是否包含"

"排序"
df.sort_index(axis=1, ascending=False)  "按照索引排序"
df.sort_values(by='B')  "按照列值排序"

"筛选"
df.loc[dates[0]]
df.loc[:,['A','B']]
df.loc['20130102':'20130104',['A','B']]
df.loc['20130102',['A','B']]
df.loc[dates[0],'A']
df.at[dates[0],'A']    "比上一条命令效率更高"
df.iloc[3]
df.iloc[3:5,0:2]
df.iloc[[1,2,4],[0,2]]
df.iloc[1:3,:]
df.iloc[:,1:3]
df.iloc[1,1]
df.iat[1,1]    "比上一条命令效率更高"
df[df.A > 0]    "Bool 筛选A列数据中大于0的行"
df[df > 0]    "Bool"
df[(df.C>0)&(df.D<0)] "使用&符号可以实现多条件筛选，当然是用"|"符号也可以实现多条件，只不过他是或的关系"
df[['A','B']][(df.C>0)&(df.D<0)]  "假如我们只需要A和B列数据，而D和C列数据都是用于筛选的"
df['A']
df[0:3]
df['20130102':'20130104']
df2 = df.copy()
df2['E'] = ['one', 'one','two','three','four','three']
df2[df2['E'].isin(['two','four'])]  "筛选特定的值"
"没有isnotin函数，取反的方法就是在函数前面加个 ~ ，好销魂的一飘"
~df.isin(other)

"去重"
data.drop_duplicates(inplace=True)
data.drop_duplicates(subset='column1')#找第一列重复者
data.duplicated(keep='last')#'first','last',False

"删除木一列"
DF= DF.drop('column_name', 1)；
DF.drop('column_name',axis=1, inplace=True)
DF.drop([DF.columns[[0,1, 3]]], axis=1,inplace=True)   # Note: zero indexed

"修改"
'.loc[row_indexer筛选条件,col_indexer赋值列名] = value'
df2.loc[df2.C>0, "D"] = 6
s1 = pd.Series([1,2,3,4,5,6], index=pd.date_range('20130102', periods=6))
df['F'] = s1
df.at[dates[0],'A'] = 0
df.iat[0,1] = 0
df.loc[:,'D'] = np.array([5] * len(df))
df2 = df.copy()
df2[df2 > 0] = -df2

"Grouping"
df.groupby(['A','B']).size().unstack().fillna(0)  "分类统计个数"
df.groupby('A').sum()
df.groupby(['A','B']).sum()
df.groupby(['job']).apply(lambda x: (x.groupby('source')
                                      .sum()
                                      .sort_values('count', ascending=False))
                                     .head(3))
df2.groupby(['E']).apply(lambda x: x.count())

"透视表pivot_table"
* 更快（一旦设置之后）
* 自行说明（通过查看代码，你将知道它做了什么）
* 易于生成报告或电子邮件
* 更灵活，因为你可以定义定制的聚合函数

"小方法"
df.set_index      "设置索引"
df.reset_index
df.to_period("M")  "设置日期索引之后，按月显示,不统计"
df.to_period("Q")  "设置日期索引之后，按季度显示,不统计"
df.to_period("A")  "设置日期索引之后，按年度显示,不统计"
df_period.index.asfreq("A")  "按年度频率显示,'A'默认是'A-DEC',其他如'A-JAN'"
df_period.index.asfreq("Q")  "按季度频率显示,'Q'默认是'Q-DEC',其他如“Q-SEP”，“Q-FEB”"
df_period.index.asfreq("M")  "按月度频率显示,'Q'默认是'Q-DEC',其他如“Q-SEP”，“Q-FEB”"
df_period.index.asfreq("B")  "按工作日显示,how='start' or end"

print(df["列名"].resample('w').sum().head())    "按周统计数据"
print(df.resample('M').sum().head())    "按月统计数据"
print(df.resample('Q').sum().head())    "按季度统计数据"
print(df.resample('AS').sum().head())    "按年统计数据"

print(df.resample('AS').sum().to_period('A'))
print(df.resample('AS').sum().to_period('Q'))
print(df.resample('AS').sum().to_period('M'))

"To dict的时候，nan 转 None"
df = pd.DataFrame({"a":[1,None],"b":[None,"foo"]})
df.where((pd.notnull(df)), None)
Out[850]: 
      a     b
0     1  None
1  None   foo
df.where((pd.notnull(df)), None).to_dict()
Out[851]: {'a': {0: 1.0, 1: None}, 'b': {0: None, 1: 'foo'}}
```

# 5. 小技巧

1. [pandas.DataFrame中删除包涵特定字符串所在的行](https://blog.csdn.net/htbeker/article/details/79645651)
思想：
将所有列转为字符串，找到所要查找的列的值的列表，对这个列表进行操作，形成新的列表，使用isin。

## [Cufflinks](https://github.com/santosjorge/cufflinks)This library binds the power of plotly with the flexibility of pandas for easy plotting

