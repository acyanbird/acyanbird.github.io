---
title: MATLAB 基础使用
date: 2021-11-05 09:34:42
categories: 学校学习
tags:
- MATLAB
---

这里记录一些以前不知道的使用



MATLAB 可以将 workspace 所有的变量存储到一个 .mat 文件中使用 `save filename` 方法，并且可以将其再度 load 使用 `load filename`方法

当然也可以只load或者存储单个

`load mydata k` `save justk k`



`clear` 清理 workspace 变量

`clc` 清理 command line window 所有输入

通过 `format` 来改变显示精度



##### matrix

matrix: [3 9] 是横着 [3;9] 是竖着

3

9

这个样子



[1 2 3 4] = [1:4] ,默认 step 为 1，更改是

[20 22 24 16] = [20:2:26]

```matlab
linspace(first,last,number_of_elements)
x = linspace(0,1,5)
x = 
    0    0.250    0.500    0.750    1.000
```



row vector to column vector using '

x = [1 2 3], x' =

1

2

3

也可以

```matlab
x = (1:2:5)'
x = 
    1
    3
    5
```

注意要使用小括号



##### 创建 matrix 的函数

rand(size), rand(2) = 创建 element 随机的 2*2 matrix

rand(2,3) 则是随机的 2*3 matrix

ones(size)，创建全部都是 1 的matrix



##### 索引 matrix

从 1 开始的

```matlab
y = A(5,7)
% A matrix 中第五行第七列
```

end 关键字，最后一行/列，end-1，倒数第二行/列

只用一个索引？先数完第一列，继续第二列

```matlab
data =

    3.0000    0.5300    4.0753       NaN
   18.0000    1.7800    6.6678    2.1328
   19.0000    0.8600    1.5177    3.6852
   20.0000    1.6000    3.6375    8.5389
   21.0000    3.0000    4.7243   10.1570
   23.0000    6.1100    9.0698    2.8739
   38.0000    2.5400    5.3002    4.4508

>> y = data(8)

y =

    0.5300

>> y = data(1)

y =

     3
```

 : 选择整个行/列 

(:,2) 选择第二列 (:,end-1:end) 选择倒数两列

用单个索引可以引用vector



读取结果

[x,y] = size(z); 将 z 的size x*y 赋值给 x 和 y 



`NaN` (or, "Not a Number") is used to represent missing data.



##### 画图

plot(x,y,方法)

title("figure名称")

legend("x1","x2","x3")	// 显示小图标，x 对应的线



numel函数

  **语法格式：**

  n = numel（A）；

  n=   numel（A，条件）；

  返回 [数组](http://baike.baidu.com/view/209670.htm) A中元素个数。若是一幅图像，则numel(A)将给出它的像素数。



##### Table

d = elements.Density

提取 element table 里面 Density 的列



##### Element wise 乘法

```matlab
C = A.*B
```



##### for 循环

```matlab
for index = values
   statements
end

以 -0.2 为步长递增，并显示值。

for v = 1.0:-0.2:0.0
   disp(v)
end

     1

    0.8000

    0.6000

    0.4000

    0.2000

     0

```



##### .*

单独一个 * 是矩阵乘法，这个是按照元素相乘

```matlab
A = [1 0 3];
B = [2 3 7];
C = A.*B
C = 1×3

     2     0    21
```



```matlab
x(x==999) = 1
```

将所有 x 里等于 999 的值替换成 1



##### Function

```matlab
function [y1,...,yN] = myfun(x1,...,xM)
```

input: x1,...,xM

output: y1, ... , yN

都可以是 vector



合并多个矩阵

C =[A B] // 横向合并

C = [A;B] // 纵向合并



**hold** on

是当前轴及图像保持而不被刷新，准备接受此后将绘制的图形，多图共存



plot3

plot 3 axis
