---
title: matlab lab1
date: 2021-11-10 12:51:35
categories: 学校学习
tags:
- MATLAB
---



这个是给予很多个点寻找最接近这个点的 func，Y = AP，当他们是 matrix（看笔记）

matlab： P = A/Y



example：

```matlab
% data points for X-axis
x = [0:0.1:1.0]';
% create the model that we will 
try and estimate
y = 2*x + 3;
% add some noise to y to make it 
more difficult realistic
ynoise = y + 0.1*randn(11,1);
% let's look at our data
figure;
plot(x,ynoise,'b+');
axis([0 1 0 5])
% 代表 x 和 y 的取值，x 0-1，y 0-5

% create matrix A
A = [x ones(11,1)];
% perform least squares using 
backslash operator
P = A\ynoise;
% get predicted y values 
using fitted model
yfit = A*P;
% plot using red line
hold on;
plot(x,yfit,’r’);
```

𝑦 = 𝑎𝑥^3+ 𝑏𝑥^2+ 𝑐𝑥 + 𝑑

```matlab
% create some random data points
m_data = 1000;
x = rand(m_data,1)*2-1;
% create 1000*1 vector with rand,0-1 之间取值
% create the model and add noise
y = x-0.3*x.^3 + (rand(m_data,1)-
1)*0.1;
% .^ power of matrix,matrix x 的三次方
% plot a graph of datapoints (blue) 
and label 
figure; plot(x,y,'b+');
xlabel('spring extension'); 
ylabel('Force');

% form A matrix (data matrix)
A = [x.^3 x.^2 x ones(m_data,1) ];
% estimate the parameters using 
backslash operator
P = A\y
% obtain predicted y values from 
model: yfit
yfit = A*P;
%plot the predicted y values on 
the same graph
hold on; plot(x,yfit,'r+');
```

#### Using MATLAB to Find x in Ax =y

For the case where A is a square matrix, we calculate the inverse using: P = inv(A)

To calculate a left-pseudo inverse: P = inv(A'*A)*A'
 To calculate a right-pseudo inverse: P = A'*inv(A*A')



总之找 Ax = y 的时候的 x, x = A\y



[v,d] = eig(A)

 eigenvectors in v and the eigenvalues in d，获得俩 matrix，V 是vector集和，d 是对角线的 eigen value



##### sum of squared errors (SSE)

SE =(ˆY−Y)T(ˆY−Y)

err = yhat - Resistance
SSE = (err)'*(err)



##### mean absolute error (MAE)

which is the mean absolute distance between the predicted and actual values

absoluteErr = abs(err);
MAE = mean(absoluteErr);



##### 寻找矩阵最大值

一个 matrix C，第一个max返回行向量，每一个列的最大值，第二个max找到这些中的最大值，

[row col] = find(C==(max(max(C))))



Find 函数，在 3×3 矩阵中查找非零元素。

```matlab
X = [1 0 2; 0 1 1; 0 0 4]
X = 3×3

     1     0     2
     0     1     1
     0     0     4
k = find(X)
k = 5×1

     1
     5
     7
     8
     9
    
```

返回的是从左上角向右数的每一个非零元素的位置

对 `X` 使用逻辑 `not` 运算符以查找零值。k2 = find(~X)

所以 find(C==1,5) 就是在 C matrix 里面寻找前五个等于 1 的值，返回它的位置



寻找某个matrix中大于特定数值的 element 的个数

比如寻找 A 里大于 10 的个数

sum(sum(A>10))

A>10 将会 produce 0 1 组成的matrix，然后一个 sum 会 return 每一个列的和，再来一个就获得了所有 1 的个数。
