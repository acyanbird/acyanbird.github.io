---
title: 通过git部署hexo
date: 2021-11-04 00:01:53
categories: 折腾
tags:
---

之前用的travis，现在还是自己折腾好了……travis还是有问题，有时间换成服务器好了

首先创造repo，然后在 _config.yml 的 deploy 里面

```
deploy:
  type: git
  repo: 你的repo地址，clone那个 ssh 版本复制过来
  branch: gh-pages // 你的git page 分支
```

git page 在 setting 里的 source 选

然后用 hexo -g d 应该就可以了

