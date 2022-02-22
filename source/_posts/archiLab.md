---
title: archiLab
date: 2022-02-15 14:12:23
categories:
tags:
---

```
int loop2N = (N / loop2Factor) * loop2Factor;
```

计算需要多少个整循环

去赋值一个 array 的 vec

```
__m128 aVec = _mm_load_ps(a+i);
```

```
__m128 yVec = _mm_set1_ps(0.0f);
设置 float，合理猜测 set 是设置整数
_mm_store_ps(ax + i, xVec);
存储到 ax+i
```



加减乘除



• _mm_sub_ps – Subtract operator
• _mm_div_ps – Divide operator
• _mm_sqrt_ps – Square root operator
• _mm_and_ps , _mm_or_ps and _mm_xor_ps – Logic operators

`cVec = _mm_add_ps(aVec, bVec);`



判断

`__m128 mask = _mm_cmpgt_ps(aVec, half); //mask = (True, False, False, True)`

cmpgt 大对小错，先vec后con

`__m128 result = _mm_blendv_ps(branchFalse, branchTrue, mask); //result = (1.0, 2.0, 2.0, 1.0)`

blendv 前错后对

```

for (i = 0; i < loopN; i+=loopFactor) {
//Load vector for a, example aVec = (0.8, 0.2, 0.3, 0.7)
__m128 half = _mm_set1_ps(0.5); //half = (0.5, 0.5, 0.5, 0.5)
__m128 mask = _mm_cmpgt_ps(aVec, half); //mask = (True, False, False, True)
//Set up vectors for if everything is True or False

__m128 branchTrue = _mm_set1_ps(1.0); //branchTrue = (1.0, 1.0, 1.0, 1.0)

__m128 branchFalse = _mm_set1_ps(2.0); //branchFalse = (2.0, 2.0, 2.0, 2.0)

//Apply mask to data (SSE4.1 only)
__m128 result = _mm_blendv_ps(branchFalse, branchTrue, mask); 
//result = (1.0, 2.0, 2.0, 1.0)
```

当你需要在判断之后赋值，like if x < 0.5 x *= -1

那么直接算所有的 *= -1 然后再 compare 赋值

`_mm_cmpgt_ps(0.5)` 对比 vec and 0.5，大于是 true 小于是 false



可以 keep 15 个以上的 reg！

bVec = _mm_add_ps(aVec, bVec);

是可以的！

使用 openmp

之前 sse 是 vector 优化，这个是 parallel 运行



export OMP_NUM_THREADS=4

用这个限制最大 thread，在编译的时候写进去应该就可吧

#pragma omp parallel for

加在 for 前面，让没有支持的 cpu 也可以跑



https://clang.llvm.org/extra/clang-tidy/checks/openmp-use-default-none.html

要在下面加上 default，应该是多线程优化中 sharing 的部分

