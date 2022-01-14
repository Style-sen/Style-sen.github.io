# 基本数据类型



## 1. 基本数据类型（32位CPU架构）

摘自[Basic data types in ARM C and C++](https://www.keil.com/support/man/docs/armcc/armcc_chr1359125009502.htm)

| 类型               | bit数 | Natural alignment in bytes | 取值范围                                                                 | 备注                                         |
| ------------------ | ----- | -------------------------- | ------------------------------------------------------------------------ | -------------------------------------------- |
| char               | 8     | 1 (byte-aligned)           | 0 to 255 (unsigned) by default.                                          | –128 to 127 (signed)编译选项`--signed_chars` |
| signed char        | 8     | 1 (byte-aligned)           | –128 to 127                                                              |
| unsigned char      | 8     | 1 (byte-aligned)           | 0 to 255                                                                 |
| (signed) short     | 16    | 2 (halfword-aligned)       | –32,768 to 32,767                                                        |
| unsigned short     | 16    | 2 (halfword-aligned)       | 0 to 65,535                                                              |
| (signed) int       | 32    | 4 (word-aligned)           | –2,147,483,648 to 2,147,483,647                                          |
| unsigned int       | 32    | 4 (word-aligned)           | 0 to 4,294,967,295                                                       |
| (signed) long      | 32    | 4 (word-aligned)           | –2,147,483,648 to 2,147,483,647                                          |
| unsigned long      | 32    | 4 (word-aligned)           | 0 to 4,294,967,295                                                       |
| (signed) long long | 64    | 8 (doubleword-aligned)     | –9,223,372,036,854,775,808 to 9,223,372,036,854,775,807                  |
| unsigned long long | 64    | 8 (doubleword-aligned)     | 0 to 18,446,744,073,709,551,615                                          |
| float              | 32    | 4 (word-aligned)           | 1.175494351e-38 to 3.40282347e+38 (normalized values)                    | `如果将int转为float可能会损失精度`           |
| double             | 64    | 8 (doubleword-aligned)     | 2.22507385850720138e-308 to 1.79769313486231571e+308 (normalized values) |
| long double        | 64    | 8 (doubleword-aligned)     | 2.22507385850720138e-308 to 1.79769313486231571e+308 (normalized values) |
| wchar_t            | 16    | 2 (halfword-aligned)       | 0 to 65,535 by default.                                                  |
| wchar_t            | 32    | 4 (word-aligned)           | 0 to 4,294,967,295 when compiled with --wchar32.                         |
| All pointers       | 32    | 4 (word-aligned)           | Not applicable.                                                          |
| bool (C++ only)    | 8     | 1 (byte-aligned)           | false or true                                                            |
| _Bool (C only)     | 8     | 1 (byte-aligned)           | false or true                                                            |

## 2. 浮点数的结构

1. 内存存储

    | 类型   | 占用内存 | 符号位 | 指数位 | 尾数位 | 指数范围   | 指数偏移 | 精度                     |
    | ------ | -------- | ------ | ------ | ------ | ---------- | -------- | ------------------------ |
    | float  | 32       | 1      | 8      | 23     | -128~127   | +127     | 约7位有效数字(1/(2^23))  |
    | double | 64       | 1      | 11     | 52     | -1023~1024 | +1023    | 约16位有效数字(1/(2^52)) |

2. 符号位：最高位，0正，1负；
3. 指数位：`为以2为底的指数+指数偏移`,这样可以表示负的指数。
4. 尾数位：`表示成以2为底的指数之后，小数点之后的二进制`。
5. 查看[浮点内存表示](https://www.h-schmidt.net/FloatConverter/IEEE754.html)
6. `浮点数表示的数值，虽然范围比较大，但是是有误差的`。

