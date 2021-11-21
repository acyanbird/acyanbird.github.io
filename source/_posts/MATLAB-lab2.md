---

title: MATLAB lab2
date: 2021-11-12 09:41:02
categories: 学校学习
tags:
- MATLAB
---

#### Creating a Cell Array

元胞数组是 MATLAB 中特有的一种数据类型，是数组的一种，其内部元素可以是属于不同的数据类型，概念理解上，可以认为它和c语言里面的结构体、[c++](https://baike.baidu.com/item/c%2B%2B/99272)里面的对象很类似



create：

`mycellarray = {'one', 'two', 'three'}`

index :

`mycellarray{n}`	获取第n个，从1开始数

可以包含不同的数据类型

`mycellarray = {'one', 'two', 'three', 5, [1 2 3]}`



#### Create table

```matlab
Team = {'Chelsea', 'Man City', 'Liverpool', 'West Ham', 'Man United'}';
Played = [11 11 10 10 11]';
Won = [8 7 6 6 5]';
Drawn = [2 2 4 2 2]';
Lost = [1 2 0 2 4]';
For = [27 22 29 20 19]';
mytable = table(Team, Played, Won, Drawn, Lost, For)
```

![](https://i.imgur.com/o2T4Vu0.png)



添加一行

```matlab
Against = [2 3 4 6 7]';
mytable.Against = Against;
```

![](https://i.imgur.com/MdG6NgU.png)



使用 for - against produce 新列 GD

`mytable.GD = mytable.For-mytable.Against`



Create the variable 'Points' and add it to your table. The points for each team are calculated as:
Points = Won*3 + Drawn.

If you didn’t know that a win was worth 3 points and a draw was worth 1 point, how could you have
worked this out using AX =Y?
Hint: you need a pseudo inverse.

A 是未知数，X 是 x1 x2，Y 是得分

![](https://i.imgur.com/VelOFDm.jpg)



##### Histogram:

当 input 是一个的时候，会根据这个 input 的值的不同 output 简单的 histogram。

histogram(titanic.Survived)

直接这样输入数组，就可以 output 关于这个 single vector 的 histogram

![](https://i.imgur.com/bOJdd4S.jpg)

然后鼠标悬停就会 show exact values

histogram2(titanic.Fare, titanic.Pclass)，三维的 histogram

#### Regression Using the MATLAB App

使用内置的 App 来训练，首先打开这个

![](https://i.imgur.com/g38K92R.png)

然后将数据集合成 table，输入 workspace 的 table，选择需要 predict 的参数

![](https://i.imgur.com/eusN9N1.png)

因为要 response resistace，所以 predictor 就是剩下的两个，然后选择 linear 模型

![](https://i.imgur.com/Qovpkzo.png)

点击旁边的training

可以在左边看到误差，RMSE 是啥来着

![](https://i.imgur.com/hJwdxEJ.png)

然后就可以对比 predict 和实际的差

![](https://i.imgur.com/ftmUekr.png)

这次是要导入 carbig，要安装插件才行，然后创建 table

`carstate = table(Acceleration,Cylinders,Horsepower, Displacement, MPG, Weight);`

这次是 predict MPG，为了找到最合适的可以选择我全都要 （不是

![](https://i.imgur.com/LosZwcI.png)

然后 matlab 会标注出误差最小的

#### Classification

也在 regression 的旁边选择 classification

![](https://i.imgur.com/p3bkc2Q.png)

看 cunfusion matrix 可以得到

![](https://i.imgur.com/aWpZicX.png)



![](https://i.imgur.com/pD28UBn.png)



![](https://i.imgur.com/ilaAYda.png)



这里要导入一个 vector 里小于 20 的数字

 lowmpg = mpg<20，会 produce 一个由 1 和 0 组成的 vector

然后将这个加入 table，这个 table 是 newcarstate

newcarstats = addvars(newcarstats, lowmpg);

这个就是在最后加入

因为

#### lustering - Finding Patterns in Data







ref:

https://baike.baidu.com/item/%E5%85%83%E8%83%9E%E6%95%B0%E7%BB%84/212876

