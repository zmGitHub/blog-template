---
title: ubuntu 14.04 搭建ionic的开发环境
date: 2015-03-27 22:41:17
tags:
  - JavaScript
  - ionic
  - App
---
　　这些天一直在研究ionic开发，发现很多大神都是在window下面开发的，但是我是在ubuntu14.04 下面进行开发，发现环境上的很多的问题和他们的不一样，所以自己总结了一下在ubuntu下面的差异，写的不好欢迎大家指导

* ubuntu14.04 jdk 安装（我的是jdk 8）
    下载linux系统下的jdk（这里就不说明了）
* android sdk 安装
1.因为android sdk 是32位的 但是我们的系统是64位所以要安装32位的运行库
```bash
$ sudo apt-get install lib32z1 lib32ncurses5 lib32bz2-1.0
```
这里注意啦！因为ubuntu13.0以后的都是上面这句代码，可是我一开始就用了下面句：
```bash
sudo apt-get install ia32-libs  ## 这是ubtun13的 更高的版本已经移除了
```
如果是这里没有安装好这个库，后面生成apk的时候就等着蛋疼吧！（因为没有任何错误信息）
2.因为我们的google被墙所以android sdk 的下载就成了问题：大家记得翻翻墙吧!

未完。。。。。
