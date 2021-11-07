---
title: hexo 部署到 github 图片无法显示
date: 2021-11-07 22:44:44
categories: 折腾
tags:
---

说实话这是在不同 blog 里面传播的模因一人有一份是吧！



https://sunnyllxx.github.io/2020/05/21/%E8%A7%A3%E5%86%B3hexo%E9%83%A8%E7%BD%B2%E6%96%87%E7%AB%A0%E5%88%B0github%E5%9B%BE%E7%89%87%E6%97%A0%E6%B3%95%E6%98%BE%E7%A4%BA%E9%97%AE%E9%A2%98/



### 问题

在 hexo 中新建一篇使用 markdown 语法文章，在文章中插入本地图片，使用绝对路径或者相对路径，再部署到 github 上面，图片无法显示。

### 解决方法

#### 1 安装插件

在博客根目录下右击选择Git Bash Here，安装第三方插件 hexo-asset-image

使用命令

```
npm install hexo-asset-image --save
```

#### 2 修改全局文件

打开博客根目录下面的_config.yml文件，修改post_asset_folder的值为true.

#### 3 新建文章

使用命令hexo new “文章名” 新建一篇文章，会发现source/_posts/目录下面会生成一个和 文章名.md  文件同名的文件夹。然后将当前这篇文章中需要插入的图片放到这个文件夹中，再到 .md文件 中引用该文件夹中的图片，使用markdown  插入图片语法! [] () ，圆括号中的地址为同名文件夹中图片的文件名（不要忘记后缀名），就可以正常查看图片了。

最后不要忘记命令

```bash
hexo clean
hexo g
hexo s
hexo d
```



就酱！

之前的话我尝试一下修改，好吧之前的修改似乎还是不行啊……放弃罢，用imgur！
