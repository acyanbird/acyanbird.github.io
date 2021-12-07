---
title: db course work
date: 2021-11-14 17:51:07
categories: 学校学习
tags:
- sql
- java
---

Right-click any area in the editor and select Reformat. Alternatively, press Ctrl+Alt+L.

format sql

有时候需要手动导入 schema 才能 insert test 来着

嗯，本来应该在本机上安装sql的……不过还是不要搞这种操作了，先搞定 maven 的依赖。已经安装好了，所以直接 open pom.xml 就可以，intelliJ 会自己搞定的

刚开始 symbol java cannot solve，打开 File Menu -> Project Structure，将 JDK 改成 11

![](http://sbytestream.pythonanywhere.com/static/blog/How-to-fix-IntelliJ-cannot-resolve-symbol/intellij-project-settings.png)



一些命令

`mvn -q exec:java@gig`

quiet mode，only produce error

exec:java 应该是compile的意思，然后后面是项目名字？大概吧



好了现在搞定远程 ssh 开发了，本地电脑通过 git 同步，和 DCS 就通过 ssh

唉之前写的那一些搞丢了吗qwq糟糕糟糕，不过没事继续写吧……

如果 mapping 的文件和远程不一致，先手动上传整个文件一次，然后就可以使用快捷键上传了。当然请记得检查是否真的上传了……



对照学校要求检查了一遍，现在就要搞定不能跨越时间的问题了

https://www.postgresql.org/docs/current/functions-datetime.html

这里查看 time 可以使用的 func，我的想法是使用

```
date_part` ( `text`, `timestamp` ) → `double precision
```

Get timestamp subfield (equivalent to `extract`); see [Section 9.9.1](https://www.postgresql.org/docs/current/functions-datetime.html#FUNCTIONS-DATETIME-EXTRACT)

```
date_part('hour', timestamp '2001-02-16 20:38:40') → 20
```

提取开场时间的 hour 和 minute，变成分钟再加上持续时间，看是否超过 24*60

https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-CHECK-CONSTRAINTS

涉及到多个条目的 check 要写在最下面

```
CREATE TABLE products (
    product_no integer,
    name text,
    price numeric CHECK (price > 0),
    discounted_price numeric CHECK (discounted_price > 0),
    CHECK (price > discounted_price)
);
```

但是 check 似乎不能直接使用 func，需要一个 view？

SELECT date_part('hour',ontime), date_part('minute',ontime) FROM act_gig as hour where actid = 2 and gigid = 50;

这样可以弄出 hour 和 min，但是怎么指定同一个呢……

 SELECT date_part('hour',ontime)*60+date_part('minute',ontime)+duration as last FROM act_gig as hour where actid = 2 and gigid = 50;

这样就可以算了！

https://www.sqlshack.com/how-to-use-sql-check-constraints/

淦，create func 好怪哦，要用 $$ 围起来

```
CREATE FUNCTION one() RETURNS integer AS $$
    SELECT 1 AS result;
$$ LANGUAGE SQL;
```

划掉，其实原始是可以的……

```sql
CREATE TABLE act_gig
(
    actID    INTEGER        NOT NULL REFERENCES act (actID),
    gigID    INTEGER        NOT NULL REFERENCES gig (gigID),
    actfee   NUMERIC(10, 3) NOT NULL,
    ontime   TIMESTAMP      NOT NULL,
    duration INTEGER        NOT NULL,
    CHECK ((date_part('hour', ontime) * 60 + date_part('minute', ontime) + duration) < 1440)
);
```

是我没有上传成功……哼唧



#### java 编写

测试 ./run.sh test 1

测试 option 1，以此类推

需要内置 data

```
$ ./run.sh reset tests/testbig.sql && ./run.sh test 1
$ ./run.sh reset tests/testbig.sql && ./run.sh test 5
$ ./run.sh reset tests/testbig.sql && ./run.sh test 6
$ ./run.sh reset tests/testsmall.sql && ./run.sh test 7
$ ./run.sh reset tests/testsmall.sql && ./run.sh test 8
```



##### task 1

line-up, using gig id, find all act on gig, line up by start time.

show actname, start time, end time

CAST can change format of the values

https://www.w3schools.com/sql/func_sqlserver_cast.asp

CAST(*expression*   AS *datatype(length)*)

```sql
SELECT act.actname                                                            AS "Act Name",
       CAST(act_gig.ontime AS TIME)                                           AS "On Time",
       CAST(act_gig.ontime AS TIME) + make_interval(mins => act_gig.duration) AS "Off Time"
from act_gig
         left join act on act_gig.actid = act.actid
WHERE gigid = 1
ORDER BY act_gig.ontime;
```



first one like this,  place holder at gigid

https://blog.csdn.net/u013159040/article/details/45605637

how to output 2d array

task 2:

首先会读到很多 array，先加上 gig，然后再加上 gig_act，如果其中有一个失败了，那就删除所有。用一个 array 存储正在添加和添加完成的，一共会有 act 相关 array + 2 （包括 gig，ticket）三个 act_gig，然后一个 for 循环删除剩下的走你

删除 id 最大的

`delete from gig where gigid in (select max(gigid) from gig);`

可以跑得通，但是需要先 del 掉所有关联外键。先 del 所有 gig_act, then is ticket, last is gig

using successAct to count how many act is successful added, and for loop to del

http://sqlines.com/postgresql/datatypes/serial

```
   INSERT INTO teams VALUES (DEFAULT, 'Manchester City');
```

serial input value is default.

https://jdbc.postgresql.org/documentation/head/8-date-time.html

传入时间使用 setobject

因为自己传入的不会自动 serial 下去，所以使用

`INSERT INTO gig VALUES ((select max(gigid) from gig)+1, 6, 'TESTING', '2018-12-11 19:00:00', 'GoingAhead');`

总之你还是手动查看吧，先看增和删是否成功

啊要先查询 venue 才行……这个不会是空

https://bbs.csdn.net/topics/300206461

可以写个 bool 判断是否成功插入，不对，这个要看

https://www.letianbiji.com/jdbc/jdbc-execute-executequery-executeupdate.html

executeQuery for SELECT，返回 ResultSet，失败大概是 null，executeUpdate for 其他,返回影响行数，失败是 0

execute 通用，返回 bool，true 是 ResultSet, false 是返回 int

对于 CREATE TABLE 或 DROP TABLE 等不操作行的语句，executeUpdate 的返回值总为零。

fail constrain 是报错，加个 try catch 就好

##### option 3

这个会预订不存在的 gig 场次，或者其他什么报错，没事啦都写好 FK 了

如果不存在就报错，所以直接 try 就可以了

##### option 4

这个是要写个 function 去 call 了说实话……

`AS` starts the definition of the method

SELECT max(ontime) FROM (SELECT * FROM act_gig WHERE gigid=14) AS sa;

这样可以找到

SELECT actid FROM act_gig WHERE gigid=14 ORDER BY ontime DESC LIMIT 1

这样更方便，limit 是只可以显示 1 个

选择先去挑序列，然后逐个传入，call func 是

https://www.postgresqltutorial.com/postgresql-jdbc/call-postgresql-stored-function/

select * from iscancel(?,?,?) 



##### option 5

创造了一大堆 view 去完成，效率什么的完全不考虑了，写出来就成



##### check

啊，似乎光写 constrain 不行，可以使用 row num 来排序，然后查看上下两场的排序就可以啦！

https://www.postgresqltutorial.com/postgresql-row_number/

partition by gigid, order 