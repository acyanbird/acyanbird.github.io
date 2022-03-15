---
title: software小组环境
date: 2022-01-17 23:17:08
categories:
tags: python
---

嗯有大腿抱就是好噗，直接 git clone 下来，用 pipenv 造一个虚拟环境，

pipenv shell

pipenv install

会检测 pipfile 进行安装

pycharm 会自动检测到环境并且导入，接下来比较麻烦的是 django 的设定。

![](https://i.imgur.com/9NC8Onk.png)

定位到根目录，然后选用 setting ，可以直接在 setting 里搜索 django 也可以在 configuration 的时候选择 fix。如果 configuration 不顺利，删除 environment variables 的 DJANGO_SETTINGS_MODULE，这次应该是 DJANGO_SETTINGS_MODULE=backend.settings。

然后是设置 pylint，本来如果是命令行用，会一个一个检查文件。使用 external tool 来配置， 使用 which pylint 来找到路径，然后输入。

/home/cyanbird/anaconda3/bin/pylint 

https://stackoverflow.com/questions/38134086/how-to-run-pylint-with-pycharm

叉叉！需要在 venv 里面重新安装，不然会报错的！使用 pipenv install pylint 安装，它会自动更新依赖的！
