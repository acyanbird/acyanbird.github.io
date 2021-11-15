---
title: db lab4
date: 2021-11-14 20:59:25
categories: 学校学习
tags:
- sql
---

#### sql view 的用法

在 SQL 中，视图是基于 SQL 语句的结果集的可视化的表。

视图是一种不存在的虚拟表: 类似表但是不是表。

- 类似表: 视图有表结构；
- 不是表: 没有数据, 视图的数据来源都是基表；

我们可以向视图添加 SQL 函数、WHERE 以及 JOIN 语句，我们也可以提交数据，就像这些来自于某个单一的表。

视图总是显示最近的数据。每当用户查询视图时，数据库引擎通过使用 SQL 语句来重建数据。

可以从某个查询内部、某个存储过程内部，或者从另一个视图内部来使用视图。通过向视图添加函数、join 等等，我们可以向用户精确地提交我们希望提交的数据。

https://zhuanlan.zhihu.com/p/80183774

```sql
SQL CREATE VIEW 实例

SQL CREATE VIEW 语法：

CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition

--创建视图--
create or replace view v_student as 
select Sno,Sname
from student;

--从视图中检索数据，即查询上面这个视图--
select * from v_student;

--也可以向查询添加条件--
select * 
from v_student
where Sname like '%云‘;

--删除视图--
drop view v_student;
```



##### 为什么要视图？

（1）简化了操作，把**经常使用**的数据定义为视图，可以将复杂的SQL查询语句进行封装。

（2）安全性，用户只能查询和修改能看到的数据。

（3）逻辑上的独立性，屏蔽了真实表的结构带来的影响。



#### Common Table Expressions (CTE)

一次性的 view，它是在单个语句的执行范围内定义的临时结果集，只在查询期间有效。它可以自引用，也可在同一查询中多次引用，实现了代码段的重复利用。



创建CTE的语法

```sql
WITH cte_name AS(
  CTE_query_definition)

注：可以同时定义1个或多个cte，做法是用逗号','隔开，最后一个cte后不跟','。
WITH cte_name1 AS(
  CTE_query_definition),
cte_name2 AS(
  CTE_query_definition)
     ...
  Main query
```

## 

#### Conversions

```
    SELECT '5'; /*This is just a string*/
    SELECT 5::text; /*This converts a number to a string*/
    SELECT '5'::decimal; /*This converts a string to a number*/
    SELECT '5'::decimal * 2; /*Once we've converted a string to a number, we can do mathematical operations with it*/
```

## 

#### Function

下面列举一些func

NOW（）获得现在的时间，可以转换SELECT NOW()::time;

```
CONCAT() 连接 txt，
SELECT commonname, CONCAT(adultweight,'g') AS weight FROM anage WHERE adultweight IS NOT NULL ORDER BY adultweight desc;
    等于
SELECT commonname, adultweight || 'g' AS weight FROM anage WHERE adultweight IS NOT NULL ORDER BY adultweight desc;
```



VIEW 和 CTE 的一个用途

因为 where 不能直接用 as 语句的别名，所以可以先创造 view 或者 cte 来节省重复输入

##### Why we can't just say WHERE alias = ...

When we write a query such as

```
SELECT commonname, maximumlongevity as lifespan FROM anage;
```

Our query cannot directly refer to lifespan, because lifespan  doesn't exist until the resultset has been generated, so this is  invalid:

```
SELECT commonname, maximumlongevity as lifespan FROM anage WHERE lifespan > 100;
```

To do this, we would need to do:

```
SELECT commonname, maximumlongevity as lifespan FROM anage WHERE maximumlongevity > 100;
```

This is particularly annoying if you have a complex expression to project — this won't work:

```
SELECT commonname, maximumlongevity / 2 as midlife FROM anage WHERE midlife > 100;
```

So you would have to repeat the expression:

```
SELECT commonname, maximumlongevity / 2 as midlife FROM anage WHERE maximumlongevity / 2 > 100;
```

One solution to this is to use a VIEW, CTE or subquery so you only need to express the calculation once:

```
        WITH anageMidlife AS (
            SELECT commonname, maximumlongevity / 2 as midlife FROM anage
        )
        SELECT * FROM anageMidlife WHERE midlife > 100;
```

A reminder that SQL is a declarative language, and the DBMS might  make various 'query plans' to actually implement the query. Just because your query has the same calculation in it twice, it doesn't necessarily mean that your query will be slower.













