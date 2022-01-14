# ORM-Sequelize


摘自[Sequelize 中文API文档](https://itbilu.com/nodejs/npm/V1PExztfb.html#definition-define)

## MODEL定义

### 时间戳

### 延时

### Getters & setters - 访问器&设置器

1. 可以在你的模型中将对象属性定义为访问/设置函数
2. 访问器&设置器有以下两种定义方式：

####  做为一个属性定义

```
class Employee extends Model {}
Employee.init({
  name: {
    type: Sequelize.STRING,
    allowNull: false,
    get() {
      const title = this.getDataValue('title');
      // 'this' allows you to access attributes of the instance
      return this.getDataValue('name') + ' (' + title + ')';
    },
  },
  title: {
    type: Sequelize.STRING,
    allowNull: false,
    set(val) {
      this.setDataValue('title', val.toUpperCase());
    }
  }
}, { sequelize, modelName: 'employee' });

Employee
  .create({ name: 'John Doe', title: 'senior engineer' })
  .then(employee => {
    console.log(employee.get('name')); // John Doe (SENIOR ENGINEER)
    console.log(employee.get('title')); // SENIOR ENGINEER
  })
```

#### 做为模型选项

```
//在下面的示例中，定义了一个名为fullName的访问器，它是对this.firstname和this.lastname两个属性引用，这个属性的一个假属性它并不是数据库中的一部分。定义假属性可以使用访问器或定义为VIRTUAL类型两种方式，Virtual类型可以验证而访问器则不能。

class Foo extends Model {
  get fullName() {
    return this.firstname + ' ' + this.lastname;
  }

  set fullName(value) {
    const names = value.split(' ');
    this.setDataValue('firstname', names.slice(0, -1).join(' '));
    this.setDataValue('lastname', names.slice(-1).join(' '));
  }
}
Foo.init({
  firstname: Sequelize.STRING,
  lastname: Sequelize.STRING
}, {
  sequelize,
  modelName: 'foo'
});

// Or with `sequelize.define`
sequelize.define('Foo', {
  firstname: Sequelize.STRING,
  lastname: Sequelize.STRING
}, {
  getterMethods: {
    fullName() {
      return this.firstname + ' ' + this.lastname;
    }
  },

  setterMethods: {
    fullName(value) {
      const names = value.split(' ');

      this.setDataValue('firstname', names.slice(0, -1).join(' '));
      this.setDataValue('lastname', names.slice(-1).join(' '));
    }
  }
});
```

### 定义字段的配置

```javascript
var Foo = sequelize.define('foo', {
  flag: { type: Sequelize.BOOLEAN, allowNull: false, defaultValue: true},    // 实例化时在没有显式设置属性值时，会自动设置为 true.设置 allowNull 选项为 false 后，会为列添加  NOT NULL 非空限制, 这意味着当执行查询（插入/更新）时相关字段为空会从数据库层抛出错误
  myDate: { type: Sequelize.DATE, defaultValue: Sequelize.NOW },    // 日期默认值 => 当前时间
  title: { type: Sequelize.STRING, allowNull: false},
  someUnique: {type: Sequelize.STRING, unique: true},    // 添加唯一（unique）约束后插入重复值会报错
  uniqueOne: { type: Sequelize.STRING,  unique: 'compositeIndex'},    // unique属性可以是boolean 或 string类型
  uniqueTwo: { type: Sequelize.INTEGER, unique: 'compositeIndex'}   // 如果为多个字段添加了相同的字符串那么将会是一个符合唯一键
  someUnique: {type: Sequelize.STRING, unique: true}     // unique属性以一个简单的简写方式创建唯一索引
  {someUnique: {type: Sequelize.STRING}},    // 同样的，也可以模型的选项中创建索引
  {indexes: [{unique: true, fields: ['someUnique']}]}
  identifier: { type: Sequelize.STRING, primaryKey: true},    // 定义一个主键
  incrementMe: { type: Sequelize.INTEGER, autoIncrement: true },    //// autoIncrement 选项用于创建一个自增的整型列
  hasComment: { type: Sequelize.INTEGER, comment: "I'm a comment!" },    // Comments 可以在MySQL 和 PG中指定定段描述
  fieldWithUnderscores: { type: Sequelize.STRING, field: "field_with_underscores" },   // 可以通过 "field" 属性来指定数据库中的字段名
  // 通过references选项可以创建外键:
  bar_id: {
    type: Sequelize.INTEGER,
    references: {
      // 引用另一个模型
      model: Bar,
      // 连接模型的列表
      key: 'id',
      // 强制使用外键约束，仅适用于 PostgreSQL
      deferrable: Sequelize.Deferrable.INITIALLY_IMMEDIATE
   }
 }
})
```

### 字段类型

```javascript
Sequelize.STRING                      // VARCHAR(255)
Sequelize.STRING(1234)                // VARCHAR(1234)
Sequelize.STRING.BINARY               // VARCHAR BINARY
Sequelize.TEXT                        // TEXT
Sequelize.TEXT('tiny')                // TINYTEXT

Sequelize.INTEGER                     // INTEGER
Sequelize.BIGINT                      // BIGINT
Sequelize.BIGINT(11)                  // BIGINT(11)

Sequelize.FLOAT                       // FLOAT
Sequelize.FLOAT(11)                   // FLOAT(11)
Sequelize.FLOAT(11, 12)               // FLOAT(11,12)

Sequelize.REAL                        // REAL        PostgreSQL only.
Sequelize.REAL(11)                    // REAL(11)    PostgreSQL only.
Sequelize.REAL(11, 12)                // REAL(11,12) PostgreSQL only.

Sequelize.DOUBLE                      // DOUBLE
Sequelize.DOUBLE(11)                  // DOUBLE(11)
Sequelize.DOUBLE(11, 12)              // DOUBLE(11,12)

Sequelize.DECIMAL                     // DECIMAL
Sequelize.DECIMAL(10, 2)              // DECIMAL(10,2)

Sequelize.DATE                        // DATETIME for mysql / sqlite, TIMESTAMP WITH TIME ZONE for postgres
Sequelize.DATE(6)                     // DATETIME(6) for mysql 5.6.4+. Fractional seconds support with up to 6 digits of precision 
Sequelize.DATEONLY                    // DATE without time.
Sequelize.BOOLEAN                     // TINYINT(1)

Sequelize.ENUM('value 1', 'value 2')  // An ENUM with allowed values 'value 1' and 'value 2'
Sequelize.ARRAY(Sequelize.TEXT)       // Defines an array. PostgreSQL only.

Sequelize.JSON                        // JSON column. PostgreSQL only.
Sequelize.JSONB                       // JSONB column. PostgreSQL only.

Sequelize.BLOB                        // BLOB (bytea for PostgreSQL)
Sequelize.BLOB('tiny')                // TINYBLOB (bytea for PostgreSQL. Other options are medium and long)

Sequelize.UUID                        //   PostgreSQL 和 SQLite 中为 UUID, MySQL 中为CHAR(36) BINARY (使用 defaultValue: Sequelize.UUIDV1 或 Sequelize.UUIDV4 生成默认值)

Sequelize.RANGE(Sequelize.INTEGER)    // Defines int4range range. PostgreSQL only.
Sequelize.RANGE(Sequelize.BIGINT)     // Defined int8range range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DATE)       // Defines tstzrange range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DATEONLY)   // Defines daterange range. PostgreSQL only.
Sequelize.RANGE(Sequelize.DECIMAL)    // Defines numrange range. PostgreSQL only.

Sequelize.ARRAY(Sequelize.RANGE(Sequelize.DATE)) // Defines array of tstzrange ranges. PostgreSQL only.

Sequelize.GEOMETRY                    // Spatial column.  PostgreSQL (with PostGIS) or MySQL only.
Sequelize.GEOMETRY('POINT')           // Spatial column with geomerty type.  PostgreSQL (with PostGIS) or MySQL only.
Sequelize.GEOMETRY('POINT', 4326)     // Spatial column with geomerty type and SRID.  PostgreSQL (with PostGIS) or MySQL only.
```

1. `DATE`直接使用JS的`DATE()`类型的数据写入。


### Validations - 验证

```
var ValidateMe = sequelize.define('foo', {
  foo: {
    type: Sequelize.STRING,
    validate: {
      is: ["^[a-z]+$",'i'],     // 只允许字母
      is: /^[a-z]+$/i,          // 只允许字母
      not: ["[a-z]",'i'],       // 不能使用字母
      isEmail: true,            // 检测邮箱格式 (foo@bar.com)
      isUrl: true,              // 检查Url格式 (http://foo.com)
      isIP: true,               // 检查 IPv4 或 IPv6 格式
      isIPv4: true,             // 检查 IPv4
      isIPv6: true,             // 检查 IPv6
      isAlpha: true,            // 不能使用字母
      isAlphanumeric: true,     // 只允许字母数字字符
      isNumeric: true,          // 只能使用数字
      isInt: true,              // 只能是整数
      isFloat: true,            // 只能是浮点数
      isDecimal: true,          // 检查数字
      isLowercase: true,        // 检查小写字母
      isUppercase: true,        // 检查大写字母
      notNull: true,            // 不允许null
      isNull: true,             // 只能为null
      notEmpty: true,           // 不能空字符串
      equals: 'specific value', // 只能使用指定值
      contains: 'foo',          // 必须包含子字符串
      notIn: [['foo', 'bar']],  // 不能是数组中的任意一个值
      isIn: [['foo', 'bar']],   // 只能是数组中的任意一个值
      notContains: 'bar',       // 不能包含子字符串
      len: [2, 10],              // 值的长度必在 2 和 10 之间
      isUUID: 4,                // 只能是UUID
      isDate: true,             // 只能是日期字符串
      isAfter: "2011-11-05",    // 只能使用指定日期之后的时间
      isBefore: "2011-11-05",   // 只能使用指定日期之前的时间
      max: 23,                  // 允许的最大值
      min: 23,                  // 允许的最小值
      isArray: true,            // 不能使用数组
      isCreditCard: true,       // 检查是有效的信用卡

      // 也可以自定义验证:
      isEven: function(value) {
        if(parseInt(value) % 2 != 0) {
          throw new Error('Only even values are allowed!')
        // we also are in the model's context here, so this.otherField
        // would get the value of otherField if it existed
        }
      }
    }
  }
});

```

### model配置

```
// 不要添加时间戳属性 (updatedAt, createdAt)
  timestamps: false,

  // 不从数据库中删除数据，而只是增加一个 deletedAt 标识当前时间
  // paranoid 属性只在启用 timestamps 时适用
  paranoid: true,

  // 不使用驼峰式命令规则，这样会在使用下划线分隔
  // 这样 updatedAt 的字段名会是 updated_at
  underscored: true,

  // 禁止修改表名. 默认情况下
  // sequelize会自动使用传入的模型名（define的第一个参数）做为表名
  // 如果你不想使用这种方式你需要进行以下设置
  freezeTableName: true,

  // 定义表名
  tableName: 'my_very_custom_table_name'
  // Enable optimistic locking.  When enabled, sequelize will add a version count attribute
  // to the model and throw an OptimisticLockingError error when stale instances are saved.
  // Set to true or a string with the attribute name you want to use to enable.
  version: true,

  // Sequelize instance
  sequelize,
```

## Model的使用

### 基本操作

通过Sequelize获取的模型对象实例列是一个DAO（Data Access Object）对象，这些对象会拥有许多操作数据库表的实例对象方法，这个对象会有create()、find()、update()、destroy()等对象操作方法，这些方法会对应的生成INSERT、SELECT、UPDATE、DELETE等SQL语句。

#### 增：create操作
create操作是指创建数据记录，我们可通过Model.create()方法添加一条数据或通过Model.bulkCreate()方法添加多条数据。
`注意 updateOnDuplicate是在插入的时候如果主键冲突就执行更新操作`

```
User.create({
  firstName: 'liu',
  lastName: '小明'
}).then(function (created){
  // 创建结果
  console.log(created);
}).catch(function(err){
  // 出错了
  console.log(err);	
})
```

在这个方法，其实际执行SQL语句类似如下：

```
INSERT INTO `user` (`id`,`first_name`,`lastName`,`createdAt`,`updatedAt`) VALUES (DEFAULT,'liu','小明','2016-05-03 15:22:06','2016-05-03 15:22:06');
```

可以看出，除了我们在User对象定义时的两个字段外，其还额外处理了id、createdAt、updatedAt三个字段。id是数据表的主键，如果在定义对象时添加了主键，将使用自定义的主键，而没有添加时将自动添加一个名为id的主键。createdAt、updatedAt两个字段同样是自动添加的，如果不需要使用可以在定义模型时进行设置：

```
sequelize.define('user', {
  firstName: {
    type: Sequelize.STRING,
    field: 'first_name'
  },
  lastName: {
    type: Sequelize.STRING
  }
}, {
  freezeTableName: true,
  'createdAt': false,
  'updatedAt': 'utime'
});
```

#### 改：update操作

1. update操作是指修改数据记录，我们可通过Model.update()方法来修改已经存在的数据记录：
2. `可以使用bulkCreate方法，批量更新`，当主键冲突时，指定需要更新的字段。

```js
db_erroressence.active_telephone.bulkCreate(valueArr,{updateOnDuplicate:["age","sex"]});  // 主键冲突，更新age和sex
```

```js
User.update({firstName:'liu'}, {where:{id:1}}).then(function (result){
  // 修改结果
  console.log(result);
}).catch(function(err){
  // 出错了
  console.log(err);	
})
```

在.update()方法中，其实际生成的SQL语句类似如下：

```js
UPDATE `user` SET `first_name`='liu',`updatedAt`='2016-05-03 15:43:32' WHERE `id` = 1
```

#### 查：find操作
find操作是指查询已存在的数据记录，我们可通过Model.findOne()方法查找一条数据，或通过Model.findAll()方法查找所有数据：

```
// 按指定条件查找一条数据
User.findOne({where:{id:1}}).then(function (result){
  // 查询结果
  console.log(result);
}).catch(function(err){
  // 出错了
  console.log(err);	
})

// 查找所有数据
User.findAll().then(function (result){
  // 查询结果
  console.log(result);
}).catch(function(err){
  // 出错了
  console.log(err);	
})
```

在上面的查询操作中，其实际执行SQL语句分别类似如下：

```
// 单条数据查询
SELECT `id`, `first_name` AS `firstName`, `lastName`, `createdAt`, `updatedAt` FROM `user` AS `user` WHERE `user`.`id` = 1

// 多条数据查询
SELECT `id`, `first_name` AS `firstName`, `lastName`, `createdAt`, `updatedAt` FROM `user` AS `user`
```

**对查询到实例可以执行save方法**

#### 删：destroy操作
destroy操作是指删除已存在的数据记录，我们可通过Model.destroy()方法来删除数据：

```
User.destroy({where:{id:1}}).then(function (result){
  // 操作结果
  console.log(result);
}).catch(function(err){
  // 出错了
  console.log(err);	
})
```

在以上.destroy()方法中，其实际生成的SQL语句类似如下：

```
DELETE FROM `user` WHERE `id` = 1
```

### model的API

#### removeAttribute() - 移除属性
#### sync() - 同步模型到数据库
#### drop() - 删除数据库中的表
#### schema() - 指定schema
#### getTableName() - 获取表名
#### addScope() - 添加限制范围
#### scope() - 应用限制范围
#### findAll() - 查询多条数据
#### findById() - 通过Id查询单条数据
#### findOne() - 查询单条数据
#### aggregate() - 聚合查询
#### count() - 统计查询结果数
统计符合查询条件的结果总数。
如果提供了include，将计算匹配关联的数目
#### findAndCount() - 分页查询
#### max() - 查询最大值
#### min() - 查询最大值
#### sum() - 求和
#### build() - 创建新实例
#### create() - 创建保存新实例
#### findOrInitialize() - 查找或初始化
#### findOrCreate() - 查找或创建
#### findCreateFind() - 查找或创建
#### upsert() - 创建或更新
#### bulkCreate() - 创建多条记录
#### truncate() - 截断模型
#### destroy() - 删除记录
#### restore() - 恢复记录
#### update() - 更新记录
#### describe() - 查询表信息


### Data retrieval / Finders - 数据索引/查找
1. 查找方法是为了从数据库中查询数据，这些方法不是返回原始数据对象，而是`返回模型实例`。因其返回的是模型实例，所以在文档的查询结果中`可以任意调用模型实例的成员、方法`等。（更多实例介绍请参考：instance）

下面是一些常用的查询方法。

```javascript
//find - 从数据库中查找一个指定元素
// 按已知id查找
Project.findByPk(123).then(function(project) {
  // project 是一个 Project 实例，且包含存储在数据中的数据
  // 当不存在 id 为123的记录时 project 为 null
})
// 按属性查找
Project.findOne({ where: {title: 'aProject'} }).then(function(project) {
  // project 是匹配到的第一个 title 为 'aProject' 的 Projects 或 null
})
Project.findOne({
  where: {title: 'aProject'},
  attributes: ['id', ['name', 'title']]
}).then(function(project) {
  // project 是匹配到的第一个 title 为 'aProject' 的 Projects 或 null
  // project 的 project.title 属性中会包含 'name'
})
```

#### findOrCreate - 从数据库中查找一个指定元素如果不存在则创建记录

1. findOrCreate可用于检测一个不确定是否存在的元素，如果存在则返回记录，不存在时会使用提供的默认值（**默认值可以为常量，也可以为变量**）新建记录。

```javascript
//如，当数据不存在时，其执行效果如下：
User
  .findOrCreate({where: {username: 'sdepold'}, defaults: {job: 'Technical Lead JavaScript'}})
  .then(([user, created])=> {
    console.log(user.get({
      plain: true
    }))
    console.log(created)
    /*
      [{
        username: 'itbilu.com',
        job: 'Technical Lead JavaScript',
        id: 1,
        createdAt: Fri Mar 22 2013 21: 28: 34 GMT + 0100(CET),
        updatedAt: Fri Mar 22 2013 21: 28: 34 GMT + 0100(CET)
      },
      true]
    */
  })
//当数据存在时，会返回记录：
User
  .create({ username: 'fnord', job: 'omnomnom' })
  .then(()=> User.findOrCreate({where: {username: 'fnord'}, defaults: {job: 'something else'}}))
  .then(([user, created]) => {
        console.log(user.get({
          plain: true
        }))
        console.log(created)

        /*
          [{
            username: 'fnord',
            job: 'omnomnom',
            id: 2,
            createdAt: Fri Mar 22 2013 21: 28: 34 GMT + 0100(CET),
            updatedAt: Fri Mar 22 2013 21: 28: 34 GMT + 0100(CET)
          },
          created: false]
        */
      })
  })
```

#### findAndCountAll - 从数据库中查找多个元素，返回数据与记录总数
1. 这个方法是findAll和count两个方法的便捷形式，这在你想使用limit和offset进行分页查询时非常有用。
2. 在返回值中，会包含以下两个属性：
    * count - 整数，匹配到的总记录数
    * rows - 对象数据，通过 limit 和 offset匹配的当前页数据

```javascript
Project
  .findAndCountAll({
     where: {
        title: {
          $like: 'foo%'
        }
     },
     offset: 10,
     limit: 2
  })
  .then(function(result) {
    console.log(result.count);
    console.log(result.rows);
  });

//findAndCountAll同样支持使用include包含，使用包含时只有将required设置为true才会添加到count部分：
User.findAndCountAll({
  include: [
     { model: Profile, required: true}
  ],
  limit: 3
});
//使用include时，两个模型之间应该存在主/外键关系，如果不存在就应该在include中手工建立连接。
```

3. 在上面的示例中，为Profile设置了required，所以在查询时会使用INNER JOIN内连接。

#### findAll - 从数据库中查找多个元素

1. findAndCountAll中使用的查询选项同样适用于findAll方法。
2. `Op = Sequelize.Op`;
3. `options`都有哪些


|Name|	Type|	Attribute|	Description|
|--|--|--|--|
|options                                          	|Object                                	|optional|描述搜索范围的选项散列|
|options.where                              |	Object                                 |optional|A hash of attributes to describe your search. See above for examples.|
|options.attributes                      |	Array<string> / Object	|optional|A list of the attributes that you want to select, or an object with include and exclude keys. To rename an attribute, you can pass an array, with two elements - the first is the name of the attribute in the DB (or some kind of 表达式 such as `Sequelize.literal`, `Sequelize.fn` and so on), and the second is 重命名
|options.attributes.include	    |Array<string>	                    |optional|包含所有属性，并加上一些额外属性. Useful for aggregations, e.g. { attributes: { include: [[sequelize.fn('COUNT', sequelize.col('id')), 'total']] }
|options.attributes.exclude	   |Array<string>	                     |optional|包含所有属性，除了一些. Useful for security purposes e.g. { attributes: { exclude: ['password'] } }

|options.paranoid	boolean	
optional
default: true
If true, only non-deleted records will be returned. If false, both deleted and non-deleted records will be returned. Only applies if options.paranoid is true for the model.

|options.include	Array<Object|Model|string>	
optional
A list of associations to eagerly load using a left join. Supported is either { include: [ Model1, Model2, ...]} or { include: [{ model: Model1, as: 'Alias' }]} or { include: ['Alias']}. If your association are set up with an as (eg. X.hasMany(Y, { as: 'Z }, you need to specify Z in the as attribute when eager loading Y).

|options.include[].model	Model	
optional
The model you want to eagerly load

|options.include[].as	string	
optional
The alias of the relation, in case the model you want to eagerly load is aliased. For hasOne / belongsTo, this should be the singular name, and for hasMany, it should be the plural

|options.include[].association	Association	
optional
The association you want to eagerly load. (This can be used instead of providing a model/as pair)

|options.include[].where	Object	
optional
Where clauses to apply to the child models. Note that this converts the eager load to an inner join, unless you explicitly set required: false

|options.include[].or	boolean	
optional
default: false
Whether to bind the ON and WHERE clause together by OR instead of AND.

|options.include[].on	Object	
optional
Supply your own ON condition for the join.

|options.include[].attributes	Array<string>	
optional
A list of attributes to select from the child model

|options.include[].required	boolean	
optional
If true, converts to an inner join, which means that the parent model will only be loaded if it has any matching children. True if include.where is set, false otherwise.

|options.include[].separate	boolean	
optional
If true, runs a separate query to fetch the associated instances, only supported for hasMany associations

|options.include[].limit	number	
optional
Limit the joined rows, only supported with include.separate=true

|options.include[].through.where	Object	
optional
Filter on the join model for belongsToMany relations

|options.include[].through.attributes	Array	
optional
A list of attributes to select from the join model for belongsToMany relations

|options.include[].include	Array<Object|Model|string>	
optional
Load further nested related models

|options.include[].duplicating	boolean	
optional
Mark the include as duplicating, will prevent a subquery from being used.

|options.order	Array | fn | col | literal	
optional
Specifies an ordering. Using an array, you can provide several columns / functions to order by. Each element can be further wrapped in a two-element array. The first element is the column / function to order by, the second is the direction. For example: order: [['name', 'DESC']]. In this way the column will be escaped, but the direction will not.

|options.limit	number	
optional
Limit for result

|options.offset	number	
optional
Offset for result

|options.transaction	Transaction	
optional
Transaction to run query under

|options.lock	string | Object	
optional
Lock the selected rows. Possible options are transaction.LOCK.UPDATE and transaction.LOCK.SHARE. Postgres also supports transaction.LOCK.KEY_SHARE, transaction.LOCK.NO_KEY_UPDATE and specific model locks with joins. See transaction.LOCK for an example

|options.raw	boolean	
optional
Return raw result. See sequelize.query for more information.

|options.logging	Function	
optional
default: false
A function that gets executed while running the query to log the sql.

|options.benchmark	boolean	
optional
default: false
Pass query execution time in milliseconds as second argument to logging function (options.logging).

|options.having	Object	
optional
Having options

|options.searchPath	           |string                       |optional    |default: DEFAULT;An optional parameter to specify the schema search_path (Postgres only)
|options.rejectOnEmpty	   |boolean / Error    |optional    |default: false;Throws an error when no records found


```javascript
// find multiple entries
Project.findAll().then(projects => {
  // projects will be an array of all Project instances
})

// search for specific attributes - hash usage
Project.findAll({ where: { name: 'A Project' } }).then(projects => {
  // projects will be an array of Project instances with the specified name
})

// search within a specific range
Project.findAll({ where: { id: [1,2,3] } }).then(projects => {
  // projects will be an array of Projects having the id 1, 2 or 3
  // this is actually doing an IN query
})

Project.findAll({
  where: {
    id: {
      [Op.and]: {a: 5},           // AND (a = 5)
      [Op.or]: [{a: 5}, {a: 6}],  // (a = 5 OR a = 6)
      [Op.gt]: 6,                // id > 6
      [Op.gte]: 6,               // id >= 6
      [Op.lt]: 10,               // id < 10
      [Op.lte]: 10,              // id <= 10
      [Op.ne]: 20,               // id != 20
      [Op.between]: [6, 10],     // BETWEEN 6 AND 10
      [Op.notBetween]: [11, 15], // NOT BETWEEN 11 AND 15
      [Op.in]: [1, 2],           // IN [1, 2]
      [Op.notIn]: [1, 2],        // NOT IN [1, 2]
      [Op.like]: '%hat',         // LIKE '%hat'
      [Op.notLike]: '%hat',       // NOT LIKE '%hat'
      [Op.iLike]: '%hat',         // ILIKE '%hat' (case insensitive)  (PG only)
      [Op.notILike]: '%hat',      // NOT ILIKE '%hat'  (PG only)
      [Op.overlap]: [1, 2],       // && [1, 2] (PG array overlap operator)
      [Op.contains]: [1, 2],      // @> [1, 2] (PG array contains operator)
      [Op.contained]: [1, 2],     // <@ [1, 2] (PG array contained by operator)
      [Op.any]: [2,3]            // ANY ARRAY[2, 3]::INTEGER (PG only)
    },
    status: {
      [Op.not]: false           // status NOT FALSE
    }
  }
})
```

#### IN/OR 等复合筛选

1. 如果要在查询中使用AND、OR或DOT等筛选条件，可以查询的where中指定$and、$or或$not等属性：
2. `在egg框架中，没有`$`运算符，所以需要使用[Op.or],[Op.and],[Op.not]`.

```
Project.findOne({
  where: {
    name: 'a project',
    $or: [
      { id: [1,2,3] },
      { id: { $gt: 10 } }
    ]
  }
})
Project.findOne({
  where: {
    name: 'a project',
    id: {
      $or: [
        [1,2,3],
        { $gt: 10 }
      ]
    }
  }
})

//这个查询生成的实际SQL语句为：
SELECT *
FROM `Projects`
WHERE (
  `Projects`.`name` = 'a project'
   AND (`Projects`.`id` IN (1,2,3) OR `Projects`.`id` > 10)
)
LIMIT 1;
$not示例：

Project.findOne({
  where: {
    name: 'a project',
    $not: [
      { id: [1,2,3] },
      { array: { $contains: [3,4,5] } }
    ]
  }
});
将生成以下语句：

SELECT *
FROM `Projects`
WHERE (
  `Projects`.`name` = 'a project'
   AND NOT (`Projects`.`id` IN (1,2,3) OR `Projects`.`array` @> ARRAY[1,2,3]::INTEGER[])
)
LIMIT 1;
```

#### 对数据集使用limit、offset、order 和 group

```
// 使用 limit 限制返回结果数
Project.findAll({ limit: 10 })

// 跳过前 10 条结果
Project.findAll({ offset: 10 })

// 跳过前 10 条结果后，返回两条数据
Project.findAll({ offset: 10, limit: 2 })
```

1. 分组与排序的语法是相似的，所以下边语法适用分组和排序。

```
Project.findAll({order: [['title', 'DESC']]})
// yields ORDER BY title DESC

Project.findAll({group: 'name'})
// yields GROUP BY name
```

2. 传入简单的字符串时，查询字符串会逐字查询，即列名不转义。如果需要列名转义，可以提供一个数组参数。

```javascript
something.findOne({
  order: [
    // will return `name`
    ['name'],
    // will return `username` DESC
    ['username', 'DESC'],
    // will return max(`age`)
    sequelize.fn('max', sequelize.col('age')),
    // will return max(`age`) DESC
    [sequelize.fn('max', sequelize.col('age')), 'DESC'],
    // will return otherfunction(`col1`, 12, 'lalala') DESC
    [sequelize.fn('otherfunction', sequelize.col('col1'), 12, 'lalala'), 'DESC'],
    // will return otherfunction(awesomefunction(`col`)) DESC, This nesting is potentially infinite!
    [sequelize.fn('otherfunction', sequelize.fn('awesomefunction', sequelize.col('col'))), 'DESC']
  ]
})
```
3. `不能在include中使用order，但是在order中可以指定Model`。参考[How do I apply order to includes](https://github.com/sequelize/sequelize/issues/4553)
4. [在Sequelize中使用group by分组聚合查询](https://itbilu.com/nodejs/npm/EJcKjQWfM.html)
#### 原始查询

默情况下，Sequlize人为查询结构创建实例，通过这个实例可以进行数据的更新、删除等操作。有时候我只需要显示数据集，而不需要进行处理，这时可以通过设置raw选项来返回原始数据：
```
// 增加 raw 选项后，会返回数据库中的原始结果
Project.findAll({ where: { ... }, raw: true })
count - 统计数据库中的元素数

count可以统计数据库中的元素数：

Project.count().then(function(c) {
  console.log("There are " + c + " projects!")
})

Project.count({ where: ["id > ?", 25] }).then(function(c) {
  console.log("There are " + c + " projects with an id greater than 25.")
})
max - 查找指定表中最大值

// 数据库中有3条记录，年龄分别是 10, 5, 40
Project.max('age').then(function(max) {
  // 会返回 40
})

Project.max('age', { where: { age: { lt: 20 } } }).then(function(max) {
  // 会返回 10
})
min - 查找指定表中最小值

// 数据库中有3条记录，年龄分别是 10, 5, 40
Project.min('age').then(function(min) {
  // 会返回 5
})

Project.min('age', { where: { age: { $gt: 5 } } }).then(function(min) {
  // 会返回 10
})
sum - 对指定属性求和

// 数据库中有3条记录，年龄分别是 10, 5, 40
Project.sum('age').then(function(sum) {
  // 会返回 55
})

Project.sum('age', { where: { age: { $gt: 5 } } }).then(function(sum) {
  // 会返回 50
})


```

### Eager loading - 预加载

1. 从数据库中加载数据时，除**数据本身外还想得到与之相关联的数据**－这就是所谓的预加载。也就是说，`使用find或findAll查询数据时，通过include属性同时加载关联的数据`。

假设有以下数据结构：

```
var User = sequelize.define('user', { name: Sequelize.STRING })
  , Task = sequelize.define('task', { name: Sequelize.STRING })
  , Tool = sequelize.define('tool', { name: Sequelize.STRING })

Task.belongsTo(User)
User.hasMany(Task)
User.hasMany(Tool, { as: 'Instruments' })

sequelize.sync().then(function() {
  // this is where we continue ...
})
```

通过belongsTo与hasMany建立关系后，就可以像下面这样查询：

```
Task.findAll({ include: [ User ] }).then(function(tasks) {
  console.log(JSON.stringify(tasks))

  /*
    [{
      "name": "A Task",
      "id": 1,
      "createdAt": "2013-03-20T20:31:40.000Z",
      "updatedAt": "2013-03-20T20:31:40.000Z",
      "userId": 1,
      "user": {
        "name": "John Doe",
        "id": 1,
        "createdAt": "2013-03-20T20:31:45.000Z",
        "updatedAt": "2013-03-20T20:31:45.000Z"
      }
    }]
  */
})
```

由于Task与User是1对多的关系，所在查询时user会做为一个对象属性被同时加载。

下面我们进行一个多对多的查询：

```
User.findAll({ include: [ Task ] }).then(function(users) {
  console.log(JSON.stringify(users))

  /*
    [{
      "name": "John Doe",
      "id": 1,
      "createdAt": "2013-03-20T20:31:45.000Z",
      "updatedAt": "2013-03-20T20:31:45.000Z",
      "tasks": [{
        "name": "A Task",
        "id": 1,
        "createdAt": "2013-03-20T20:31:40.000Z",
        "updatedAt": "2013-03-20T20:31:40.000Z",
        "userId": 1
      }]
    }]
  */
})
```

在多对多的关系中，相关数据会做为一个数组属性被同时加载。

关联查询时，可以使用as选项为关系数据指定别名：

```
User.findAll({ include: [{ model: Tool, as: 'Instruments' }] }).then(function(users) {
  console.log(JSON.stringify(users))

  /*
    [{
      "name": "John Doe",
      "id": 1,
      "createdAt": "2013-03-20T20:31:45.000Z",
      "updatedAt": "2013-03-20T20:31:45.000Z",
      "Instruments": [{
        "name": "Toothpick",
        "id": 1,
        "createdAt": null,
        "updatedAt": null,
        "userId": 1
      }]
    }]
  */
})
```

2.  关联查询时，同样可以使用where选项对关联数据进行筛选（所有的where为与的关系）：


```
User.findAll({
    include: [{
        model: Tool,
        as: 'Instruments',
        where: { name: { $like: '%ooth%' } }
    }]
}).then(function(users) {
    console.log(JSON.stringify(users))

    /*
      [{
        "name": "John Doe",
        "id": 1,
        "createdAt": "2013-03-20T20:31:45.000Z",
        "updatedAt": "2013-03-20T20:31:45.000Z",
        "Instruments": [{
          "name": "Toothpick",
          "id": 1,
          "createdAt": null,
          "updatedAt": null,
          "userId": 1
        }]
      }],

      [{
        "name": "John Smith",
        "id": 2,
        "createdAt": "2013-03-20T20:31:45.000Z",
        "updatedAt": "2013-03-20T20:31:45.000Z",
        "Instruments": [{
          "name": "Toothpick",
          "id": 1,
          "createdAt": null,
          "updatedAt": null,
          "userId": 1
        }]
      }],
    */
  })
```

注意：`使用include.where条件时，include.requied会被隐式的设置为true，即在查询时会使用INNER JOIN内连接`可以显性地设为false。

全关联

如果多个模型间存在关联关系，而我们在查询时又要查询所有的数据，就可以设置all: true来关联所有模型：

User.findAll({ include: [{ all: true }]});
包括软删除的数据

如果要在结果中包含软删除的数据，请将include.paranoid设置为true：

User.findAll({
    include: [{
        model: Tool,
        where: { name: { $like: '%ooth%' } },
        paranoid: true // 查询并加载软删除的数据
    }]
});
预加载关联数据的排序

在1对多（one-to-many）的关系中：

Company.findAll({ include: [ Division ], order: [ [ Division, 'name' ] ] });
Company.findAll({ include: [ Division ], order: [ [ Division, 'name', 'DESC' ] ] });
Company.findAll({
  include: [ { model: Division, as: 'Div' } ],
  order: [ [ { model: Division, as: 'Div' }, 'name' ] ]
});
Company.findAll({
  include: [ { model: Division, as: 'Div' } ],
  order: [ [ { model: Division, as: 'Div' }, 'name', 'DESC' ] ]
});
Company.findAll({
  include: [ { model: Division, include: [ Department ] } ],
  order: [ [ Division, Department, 'name' ] ]
});
在多对多（many-to-many）的关系中同样可以使用排序：

Company.findAll({
  include: [ { model: Division, include: [ Department ] } ],
  order: [ [ Division, DepartmentDivision, 'name' ] ]
});
嵌套预加载

可以在关联模型中嵌套预加载关系模型：

User.findAll({
  include: [
    {model: Tool, as: 'Instruments', include: [
      {model: Teacher, include: [ /* etc */]}
    ]}
  ]
}).then(function(users) {
  console.log(JSON.stringify(users))

  /*
    [{
      "name": "John Doe",
      "id": 1,
      "createdAt": "2013-03-20T20:31:45.000Z",
      "updatedAt": "2013-03-20T20:31:45.000Z",
      "Instruments": [{ // 1:M and N:M association
        "name": "Toothpick",
        "id": 1,
        "createdAt": null,
        "updatedAt": null,
        "userId": 1,
        "Teacher": { // 1:1 association
          "name": "Jimi Hendrix"
        }
      }]
    }]
  */
})
这会生成一个外连接，但where子句的关系模型会使用内连接并返回唯一一个子句：

User.findAll({
  include: [{
    model: Tool,
    as: 'Instruments',
    include: [{
      model: Teacher,
      where: {
        school: "Woodstock Music School"
      },
      required: false
    }]
  }]
}).then(function(users) {
  /* ... */
})
include同样支持嵌套加载：

User.findAll({ include: [{ all: true, nested: true }]});


### API

##### build() - 创建新实例
1. 参数，为一个字典。
```
build(values, [options]) -> Instance
```
创建一个新的模型实例，Values参数为新例指定的键值对对象

##### create() - 创建保存新实例
```
create(values, [options]) -> Promise.<Instance>
```
构建一个新的模型实例，并进行保存。与build()方法不同的是，此方法除创建新实例外，还会将其保存到对应数据库表中。

## 关联
1. 有四种关联关系：
    * BelongsTo
    * HasOne
    * HasMany
    * BelongsToMany

### 基本概念

#### 源模型和目标模型

`User.hasOne(Project);` User是源模型，Project是目标模型。

#### 外键 


### 一对一

#### BelongsTo - 属于

1. BelongsTo关联表示一对一关系的外键存在于源模型。
2. 如，下例中Player是通过外键关联的Team的一部分：

```
var Player = this.sequelize.define('player', {/* attributes */})
  , Team  = this.sequelize.define('team', {/* attributes */});

Player.belongsTo(Team); // 会为Player添加一个teamId 属性以保持与Team 主键的关系
```

##### 外键

1. 默认情况下，一个属于关系的外键将从目标模型的名称和主键名称生成。
2. 默认命名使用驼峰式命名，而在源模型中添加了underscored: true配置，将使用蛇型命名。
3. 在定义中使用as命名时，会将其做为目标模型的名称.
```
User.belongsTo(UserRole, {as: 'role'}); // 会为 user添加 roleId 属性而不是 userRoleId
```

#### HasOne - 拥有一个

1. HasOne关联表示一对一关系的外键存在于目标模型。

#### HasOne 与BelongsTo 的不同

1. 在1:1 的关系中，可使用HasOne 或BelongsTo来定义。但它们的使用场景有所不同。

### 一对多(One-To-Many)关联

1. One-To-Many关联是指一个源模型连接多个目标模型。反之目标模型都会有一个明确的源。
```
var User = sequelize.define('user', {/* ... */})
var Project = sequelize.define('project', {/* ... */})
// 定义 hasMany 关联
Project.hasMany(User, {as: 'Workers'})
```
会向 User 中添加一个projectId或project_id属性。Project 的实例中会有访问器getWorkers 和 setWorkers。这是一种单向关联方式，如果两个模型间还有其它关联方式请参考下面的多对多关系。

### 多对多(Belongs-To-Many)关联

1. Belongs-To-Many 关联是指一个源模型连接多个目标模型。而且，目标模型也可以有多个相关的源。

```
User.belongsToMany(Project, { as: 'Tasks', through: 'worker_tasks', foreignKey: 'userId', otherKey: 'projectId'})
```

```
User.findAll({
  include: [{
    model: Project,
    through: {
      attributes: ['createdAt', 'startedAt', 'finishedAt'],
      where: {completed: true}
    }
  }]
});
```

## 踩过的坑
1. 查询出来的model集，如果使用attributes指定了重命名，不能直接使用对象的访问符获取元素，只能使用dataValues，没有重命名就可以使用`.`。
