---
title: dblab2
date: 2021-10-26 16:45:06
categories: 学校学习
tags:
---

让我们复习一下基本语句

```bash
/modules/cs258/bin/psql postgres
```

连接

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

##### data type numeric 问题

numertic 是 def by (size,d)，但是需要注意，这个size是包括小数点的，比如说最多存储 10，小数点两位，那么就是 numeric(4,2)

##### count() 函数

SQL `COUNT`函数是一个聚合函数，它返回符合条件行数。 可以使用[SELECT语句](http://www.yiibai.com/sql/sql-select.html)中的`COUNT`函数来获取员工数量，每个部门的员工数量，指定工作岗位的员工数量等。

以下是SQL `COUNT`函数的语法：

```sql
COUNTC ([ALL | DISTINCT] expression);
```

所以

`select count(*) from anage;` 就是返回所有行数。

##### JDBC 使用

这个就大概能用就行了（嚼

拿其中一个举例子

```java
private static void showAllMoreThan1000Years(Connection conn){
        String selectQuery = "SELECT commonname,maximumlongevity FROM anage WHERE maximumlongevity > 1000";
    // def format
        try{
            PreparedStatement preparedStatement = conn.prepareStatement(selectQuery);
            //给传输赋值select，直接运行
            ResultSet longLives = preparedStatement.executeQuery();
            // return 所有的结果
            while(longLives.next()){
                String commonname = longLives.getString(1);
                float maxAge = longLives.getFloat(2);
                System.out.println(maxAge + ", " + commonname);
            }
        }catch(SQLException e){
            System.err.format("SQL State: %s\n%s\n", e.getSQLState(), e.getMessage());
            e.printStackTrace();            
        }
    }
```



getstring?

**getString**表示以 Java 编程语言中String的形式获取此 ResultSet对象的当前行中指定列的值。   

**参数：**

columnIndex - 第一个列是 1，第二个列是 2，……

**返回：**

列值；如果值为 SQL NULL，则返回值为 null



当然也有getint，这里的getfloat，指的是获得制定列中int/float...的值，没有返回 null。





要实现的

- [ ] Count the number of species that have an adult weight greater than 4000g.

`select count(*) from anage where anage.adultweight > 4000;`



- [ ] Find the common name of all species that have a litter/clutch size greater than 40000. Make sure you only project out the CommonName  column (not other columns too).
- [ ] Find the species name and common name of species that have an adultweight greater than 200 times their birthweight.
- [ ] Change the weight of 'Apis mellifera' ('Honey bee') to be 3g.
- [ ] Insert a (maybe fictional) animal of your choice (or one that  doesn't appear in the dataset already — there don't seem to be many  insects).
- [ ] Delete your newly inserted animal.



ref:

https://zhuanlan.zhihu.com/p/43802865

https://www.yiibai.com/sql/sql-count.html

https://blog.csdn.net/Thinkingcao/article/details/69950314

https://www.cnblogs.com/kenx/p/13553931.html
