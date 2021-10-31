---
title: dblab2
date: 2021-10-26 16:45:06
categories: 学校学习
tags:
---

让我们复习一下基本语句

CREATE TABLE 

```sql
CREATE TABLE 表名称
(
列名称1 数据类型,
列名称2 数据类型,
列名称3 数据类型,
....
)
```

数据类型包括



| 数据类型                                             | 描述                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| integer(size) int(size) smallint(size) tinyint(size) | 仅容纳整数。在括号内规定数字的最大位数。                     |
| decimal(size,d) numeric(size,d)                      | 容纳带有小数的数字。 "size" 规定数字的最大位数。"d" 规定小数点右侧的最大位数。 |
| char(size)                                           | 容纳固定长度的字符串（可容纳字母、数字以及特殊字符）。 在括号中规定字符串的长度。 |
| varchar(size)                                        | 容纳可变长度的字符串（可容纳字母、数字以及特殊的字符）。 在括号中规定字符串的最大长度。 |
| date(yyyymmdd)                                       | 容纳日期。                                                   |

可以在创建表时规定约束（通过 CREATE TABLE 语句），或者在表创建之后也可以（通过 ALTER TABLE 语句）。

我们将主要探讨以下几种约束：

- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- DEFAULT

这些加在数据类型之后



唔，mvn， java 操纵……挺麻烦的嘛~

ref:

https://zhuanlan.zhihu.com/p/43802865
