---
layout: post
title: Spark本地版-Win10+虚拟机linux
tags: spark 
categories: spark
---

Spark本地版-Win10+虚拟机linux
================

写在前面
--------

最近动起了学spark的念头，于是准备配置一个试试。作为计算机菜鸟几天来踩坑不少。

关于网上推荐spark进阶书籍和路线，好多人说看官方文档就够了，而我必须说，

**信了你们的鬼话！！**

什么 *零基础入门Spark* 、*在Linux虚拟机上配置Spark傻瓜教程* ，喵的一步步做为什么就光报错？？

（唉？？心虚地看了眼标题）

从图书馆随手预约了一本书迟迟未到，在我艰难的一步一个脚印踩着坑终于配置好了本地版（对，小菜鸡用了三天才配了个本地版）时，书到了，然后我翻开目录：

**spark本地单机模式体验-安装虚拟机-安装JDK-下载Spark预编译包……**

**高可用Spark分布式集群部署-集群总览-机器型号选择-……部署SparkStandalone集群……**

不说了，都是泪。这本书-《Spark最佳实践》by陈欢、林世飞

交代一下环境：Win10，虚拟机VMWare搭Ubuntu16.04.3

Spark的配置模式
---------------

Spark有好几种模式：

1.  本地调试版，就是专门用来调试代码的
2.  伪分布式部署，其实没有集群，自己在PC上装好几个虚拟机，假装自己是大户，连起来玩
3.  集群(集群也有好几种)

Windows上安装本地版Spark
------------------------

### 配置JDK

通常提到JAVA都会分JDK和JRE。

JDK是JAVA的开发者工具套件，包括JRE；JRE则是运行JAVA脚本程序所需要的虚拟机环境。

直接百度JDK，下载对应的版本，window是exe安装程序，没什么可说的。

有个小坑：1.8版本以前JDK安装完目录下都有一个JRE文件夹，但是最新发布的1.9版本将JRE里面的Classes全都整合模块化了，于是JRE路径消失……

之前我装了1.9，再部署Spark的时候老是说 xxx找不到，不知道是不是这个原因，反正后来我用了1.8的JDK，部署好后也没有精力再去验证。

### 准备Spark和Hadoop

各路说法说spark本地版不需要弄Hadoop的。。。

安装spark后在目录里能发现一个叫Jars的文件夹，目测里面就是Spark依赖的Hadoop相关文件 但是 **不全** ，启动后会报警，各种 **找不到对象** ，强迫症简直不能忍。而且做伪分布的话还是需要一个完整的hadoop的，咸鱼也要有梦想。

去官网上下载spark的预编译包，然后注意名字with-hadoop-xx注明了对应的hadoop的版本，再到hadoop官网上下载一个相对应的版本。（不给传送门了自己找吧）

然后这两个都是压缩包，腾个地方解压就行了。

### winutils

[传送门](https://github.com/steveloughran/winutils) 这个东西用来解决权限问题，windows下可能运行spark可能发生“空指针”问题（虽然我并不懂这啥意思）

下载 **winutils.exe** 放到`.\hadoop\bin\`目录下面，然后在这个目录下使用powershell运行。

    winutils.exe chmod 777 /tmp/hive 

### 配置环境变量

主要考虑两个东西：Path和环境变量

折腾了好几天自己对这些东西有点见解，不知道对不对

我们这边要用调用到的程序，都要把所在路径配到Path里面去，另外还得新建几个环境变量

-   Java：window里JAVA好像会自己配置Path（如果没有就做到`...\Jdk\bin`这一层，`...\jdk\jre`也扔进去），但另需设置两个环境变量（系统变量页，新建变量）：`JAVA_HOME=.\jdk`(JAVA安装路径);`CLASSPATH=.\jdk\lib`(JAVA类文件)

-   Spark: 首先把 `.\spark\bin`扔到Path里，然后`SPARK_HOME=.\spark`

-   Hadoop: Path-`.\hadoop\bin`;新建`HADOOP_HOME=.\hadoop`

记住环境变量就按这个名字命名。按理说都配到Path里小电脑应该啥都能找到了，但是这几个HOME其实是Spark要访问的。环境变量说白了也就是所有程序都能访问的全局变量。

### 配置文件

到 `.\spark\conf`目录下，里面有好几个`.template`结尾的文件，备份一份，然后把末尾`.template`删掉

主要是 `spark-env.sh` `spark-default.conf` `slaves`

### 测试

打开cmd，输入spark-shell

    Spark context available as sc.
    SQL context available as sqlContext.

出现这两句的话就说明成功啦。

虚拟机Linux的配置方法
---------------------

### 解压

菜鸡之前摸都没摸过linux，第一次入门，关于虚拟机安装linux过几天再写。为了完整性写一下linux安装spark

首先必须要的 JDK1.8（linux的也是压缩包），Hadoop，Spark都备好 （要用pyspark的话linux自带python，喜欢用anaconda也可以下一个，安装方法都差不多）

然后通过共享文件夹传到虚拟机里面，找个地方解压好，建议在home目录下找个地就好了，不然还有权限问题有点纠结。

linux解压通过命令

    tar -zxvf 路径/文件名 -C 目标路径

得到三个目录。

感觉linux安装东西就是 解压安装包-配置环境变量

### 环境变量

环境变量写到这里：`ctrl+alt+t`唤出shell

    sudo vi /etc/profile

或者

    sudo vim /etc/profile

这些命令的含义：

-   sudo-管理员权限操作；
-   vi/vim-一个文本编辑器(如果没有vim可以通过 `sudo apt-get install vim` 安装)
-   vi默认只读模式打开，可以用上下左右移动光标，移到需编辑位置按`i`启动编辑模式，修改完用`esc`返回只读模式；
-   全部改完后，在只读模式下输入`:q`退出，`:q!`不保存强制退出，`:w`保存，':wq'保存并退出。

![环境变量配置](https://github.com/WangLiuying/WangLiuying.github.io/blob/master/_posts/pic/2017-12-20-1.png?raw=true)

配置如上图

如果安装了Anaconda，要把Anaconda的bin目录连在PATH之前，优先级比较高

### 配置文件

进入到`./spark/conf`目录里面

复制文件用

    cp (路径/)文件名 新名

然后用 `vi spark-env.sh`

仔细看注释的字，是些配置说明

这边本地版写上

    export SPARK_LOCAL_IP=虚拟机ip
    export SPARK_WORKING_MEMORY=2G

### 测试运行

唤出shell 也要出

    Spark context available as sc.
    SQL context available as sqlContext.

才算安装好
