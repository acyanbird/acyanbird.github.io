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

