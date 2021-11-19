---
title: matlab 2
date: 2021-11-18 15:03:29
categories:
tags:
---



cluster data 需要输入应该处理的数据，必须是 matrix

[idx,C] = kmeans(X, k);



idx 是输出的数据集，C 是每个集的 center，一个 matrix，X 是输入的 matrix，k 是你打算分成多少块

gscatter(x,y,idx);

这个是画图，x 和 y 可以是输入 matrix 的两列



plot digits

这个提供了一个大表格，里面一列有 784 个像素表示 1 和 0，然后你需要将这个变成 28*28 的 matrix，然后画出来，一共画 15 个



subplot 在一个大图里面 plot 一堆小图

reshape

**B = reshape(A,m,n)**

返回一个m*n的[矩阵](https://baike.baidu.com/item/矩阵)B， B中元素是按列从A中得到的。如果A中元素个数没有m*n个， 则会引发错误。

```
for c = 1:15
```

从 1 到 15

imshow(I) 显示图片

```matlab
mn = table2array(mnisttrain)
for x=1:15
    subplot(5,3,x); % 一共 15 个，正在做第 x 个
    img = reshape(mn(x,:),28,28)'; % 不知道为什么图像 90 度
    imshow(img);
end
```

