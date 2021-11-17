---
title: java 关联知识
date: 2021-11-14 17:33:42
categories: 学校学习
tags:
- sql
- java
---

为了写 db 的 course work，把 java 相关的知识康一下……不然没法 debug

### jvm jre jdk

https://zhuanlan.zhihu.com/p/48487801

JVM + 核心类库 = JRE

JRE + java开发工具（javac.exe/jar.exe) = JDK

#### JVM 

JVM 是保证 JAVA 可以跨平台执行的虚拟机

![](https://pic2.zhimg.com/80/v2-d251cbe1cc073d3ae829fd6beeb8d899_720w.jpg)

本来我们编写的Java代码计算机还是不认识的，但是我们在每一个操作系统上都会配置一个与之相对应的JVM，会帮我们把我们的Java代码翻译成对应操作系统可以识别的内容。

所以说我们在第一次写Java程序时都要先把JVM给装好。



#### JRE

Java Runtime Environment 即Java运行环境

JVM + 核心类库 = JRE

刚才不是说只需要装JVM吗？那这个JRE是个什么鬼东西？

是因为只有JVM不能运行，它还需要核心类库，才能保证Java运行

由于JRE包含JVM 因此我们只要直接安装JRE 就顺便把JVM安装了

![](https://pic1.zhimg.com/80/v2-0dcc3179b5d9295bf6f2d9548a2404a4_720w.jpg)



#### JDK

JDK(Java Development Kit)，给程序员用的，开发 java 的工具

![](https://pic4.zhimg.com/80/v2-b53fd4f716e3b299537a631dfb422e0b_720w.jpg)

不过在 11 里 jdk 和 jre 统一了。



### MAVEN

我觉得是时候了解一下 maven 是如何运作的了……

https://www.liaoxuefeng.com/wiki/1252599548343744/1309301146648610

Maven就是是专门为Java项目打造的管理和构建工具，它的主要功能有：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制。



一个使用Maven管理的普通的Java项目，它的目录结构默认如下：

```ascii
a-maven-project
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   └── resources
│   └── test
│       ├── java
│       └── resources
└── target
```

项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构。



剩下的涉及好多，要用的时候再说吧……

