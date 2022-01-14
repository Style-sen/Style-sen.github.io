# PHP-基础


## 数据类型

### array
1. 数组(键为数字)和字典（键值对）都是array。

#### 方法

##### array_merge
1. 如果这两个数组中有相同的字符串键名，后者将替换前者。
2. 但如果使用的是array_merge_recursive()则可保留，并作一个子数组存在。
3. 如果数组中包含相同的数字键名，则后面的不会覆盖前面的值，而是后面的键值按顺序依次增加，附在后边。

## ThinkPHP

### 使用LOG
```php
use think\Log;
Log::record('[ WECHAT ] ' . var_export($temp_teturn_arr, true), 'info');  // 打印变量

```

### 使用环境变量
使用`getenv`


