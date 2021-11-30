---
title: dblab3
date: 2021-11-14 17:24:28
categories: 学校学习
tags:
- sql
---

通过 \c natrue 连接到数据库，然后 \i nature-completed.sql 来创建数据库。

join 中 on 和 where 的区别

https://www.runoob.com/w3cnote/sql-join-the-different-of-on-and-where.html

数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户。

在使用 left jion 时，**on** 和 **where** 条件的区别如下：

- 1、 **on** 条件是在生成临时表时使用的条件，它不管 **on** 中的条件是否为真，都会返回左边表中的记录。
- 2、**where** 条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有 left join 的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。



假设有两张表：

**表1：tab1**

| id   | size |
| ---- | ---- |
| 1    | 10   |
| 2    | 20   |
| 3    | 30   |

**表2：tab2**

| size | name |
| ---- | ---- |
| 10   | AAA  |
| 20   | BBB  |
| 20   | CCC  |

两条 SQL:

```
select * from tab1 left join tab2 on (tab1.size = tab2.size) where tab2.name='AAA'

select * from tab1 left join tab2 on (tab1.size = tab2.size and tab2.name='AAA')
```

![](https://i.imgur.com/DtsMFdP.png)



![](https://i.imgur.com/SsNGZjS.png)

其实以上结果的关键原因就是 left join、right join、full join 的特殊性，不管 **on** 上的条件是否为真都会返回 **left** 或 **right** 表中的记录，**full** 则具有 **left** 和 **right** 的特性的并集。 而  inner jion 没这个特殊性，则条件放在 **on** 中和 **where** 中，返回的结果集是相同的。



https://www.w3school.com.cn/sql/sql_view.asp



还有 natrual join

https://www.postgresqltutorial.com/postgresql-natural-join/

A natural join can be an [inner join](https://www.postgresqltutorial.com/postgresql-inner-join/), [left join](https://www.postgresqltutorial.com/postgresql-left-join/), or right join. If you do not specify a join explicitly e.g., `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN`, PostgreSQL will use the `INNER JOIN` by default.

所以 natrual join 是自动根据名字相同的列执行 JOIN，如果有名字相同的列，根据不同 join 来选择，然后这个列两个表相同的合并

