# PROGRAM SQL



## 1. 创建表

```sql
-- 【SQLITE】
CREATE TABLE COMPANY
( 
    ID INT PRIMARY KEY, -- 主键，唯一索引
    NAME TEXT NOT NULL UNIQUE, -- UNIQUE 唯一索引
    AGE INT NOT NULL , 
    ADDRESS CHAR(50), 
    SALARY REAL DEFAULT 50000.00,
    -- UNIQUE(column_name1,column_name2)  -- 联合唯一
    -- primary key (id,bh) -- 联合主键
);
```

## 2. 新增

1. `ALTER TABLE <表名> ADD <新字段名><数据类型>[约束条件];`新增字段。
2. `INSERT INTO`
3. 【SQLITE】`INSERT OR REPLACE INTO table-name (column-name,...) VALUES (column-value,...)`需要配合唯一索引进行判断，只有当唯一索引都没有的情况下，才进行新增。否则更新，其他列都更新，没有的为NULL。
4. 【SQLITE】`INSERT OR IGNORE INTO`

## 3. 删除

1. 删除`DELETE FROM table_name WHERE [condition];`
2. 删除所有行`DELETE FROM table_name`；【SQLITE】

## 4. 修改

1. 更新`UPDATE table_name SET column1 = value1, column2 = value2...., columnN = valueN WHERE [condition];`【SQLITE】

## 5. 查询

### 5.1. 统计函数

#### 5.1.1. `count()`用于行数的统计

1. count()列值为空时认为没有这一行；
2. count()为空时返回结果为0

#### 5.1.2. `sum()`用于求和

1. sum()列值为空时不计算；.。
2. sum()为空时返回结果为null；

### 5.2. `GROUP BY`

1. 结合统计函数,根据一个或多个列对结果集进行分组.【SQLITE】

