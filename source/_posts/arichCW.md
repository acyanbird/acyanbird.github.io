---
title: architecture course work
date: 2022-02-22 15:10:45
categories:
tags:
---

这个因为 specify 了学校目录所以都放那个机子上跑，所以需要使用 SILO=1 和 OMP_NUM_THREADS=6，加到 bashrc 里面就可

然后要在本地安装 visit

https://visit-sphinx-github-user-manual.readthedocs.io/en/latest/Intro/Installing_VisIt.html

这个还是比较麻烦的，下载相应版本的 targz 包，然后再下载 install script

https://wci.llnl.gov/simulation/computer-codes/visit/executables

添加权限 `chmod +x visit-install`，使用 `visit-install version platform directory` 放在和 targz 同目录下，给的范例是

`visit-install 3.0.0 linux-x86_64 /usr/local/visit` 不修改安装地点，看这个地方要 sudo 了

然后添加到 path，在 ~/.bashrc 最后一行加入 `export PATH="/usr/local/visit/bin:$PATH"`

然后直接输入 visit 就可以运行，出现了找不到 py 的问题，我是 ubuntu 20 所以安装 `sudo apt install python-is-python3` 就可以启动了

使用 visit 的话，左边 open file 打开 silo 的那个文件夹，记得 visit 要在 ACACGS 的目录下打开这样好找。然后下面的 plot, add - volume x_nodal or p_nodal.

 先使用 AVX 搞 ddot waxpby sparsemv

补充一下，不能自动从 deployment 那里下载新增文件，所以直接在 tool - deployment - browse remote host  打开下载文件

决定使用 AVX，反正这个也快一些。先处理 ddot，256 位寄存 64 的 double 精度，一共可以存四个

```
__m256d localResult = _mm256_setzero_pd();
__m256d xi = _mm256_load_pd(x + i);
```

加个 d 是代表 double

草，记住 jb 的 deployment 的第二个是相对路经啊！

尝试 remote 开发？https://www.jetbrains.com/help/clion/remote-projects-support.html#remote-toolchain

file - build/execute- toolchain choose remote

算了不要弄了，一会儿还得用 remote host的呢

在 cppflags 里面加入 -fopenmp -mavx 

这次一个 vector 包含 4 个 double，将 4 个加起来 

```
__m256d x;
__m256d s = _mm256_hadd_pd(x,x);
return ((double*)&s)[0] + ((double*)&s)[2];
```

因为总是出现异常访问问题所以用远程 deployment 试试看

https://youtrack.jetbrains.com/issue/CPP-24324

在 ssh 的 keepalive 要取消，或者设置成 300？也不知道为什么这样就能连的上……

然后参考这个

https://www.jetbrains.com/help/clion/remote-projects-support.html?keymap=secondary_intellij_idea_classic#Makefile-toolchain

说句实在话真的超级麻烦啊啊啊啊！牙白！

在 toolchain 设置好 remote 之后，在 makefile 选中（都在 setting 里的 build 里面搞）,然后 makefile 指定本地的文件，修改好 deployment 的 mapping，会自动导航到 remote 的 executable 去？是的，自动的。有时候刚刚打开会连接不上远程服务器，重新设置一下 setting 里的 deployment 就可以了

使用 debug 可以找到问题了，如果不行就重启罢（无慈悲



如果看 log 的话在 help 里面 show log in file 那里。





`vmovapd (%rax),%ymm0` 这个时候 segmentation fualt，访问 x ptr 的时候……出现了问题，访问方式出现了错误？

回到 sse 进行访问，求和的话 https://stackoverflow.com/questions/6996764/fastest-way-to-do-horizontal-sse-vector-sum-or-other-reduction

使用 `v = _mm_hadd_pd(v, v);` 

然后 store 低位的 64 位 `double re;  _mm_storel_pd(&re, sum);`

这样加起来就可以了

 
