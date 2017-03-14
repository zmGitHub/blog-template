---
title: react-native 项目初始化问题
date: 2016-11-23 09:01:41
tags:
  - react
  - reactNative
  - App
---
从 ionic 跳到 react-native 具体哪个好用还没有深入的实践过, 今天兴高采烈的来试试 react-native 却发现挺多问题的:
* init 项目好慢:
```bash
这基本都是网络的问题, 所以将npm仓库源替换为国内镜像:
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
改完后记得测试下:
npm info react 可以看到仓库源是淘宝的了

然后重新 init 记得加上 --verbose 看信息
```
* 项目init 后一直卡着不懂:
```bash
这个问题感觉比较坑, 完全没有任何错误信息,后来不停的找资料发现其实是 watchman 的问题导致的, 所以这就好解决了:
rm -rf /usr/local/var/run/watchman/ && brew uninstall watchman && brew install watchman
跑完后继续 init, 然后发现不会卡住了
```

* watchman 一直重新连接
```bash
[sane] Warning: Lost connection to watchman, reconnecting..
```
你会发现不管怎么变动文件内容你模拟器里面都没有任何变化, 这个也是 watchman 的问题,我在 Google 上看了下基本的解决方法是:
```bash
$ brew install rmtree
$ brew rmtree watchman
$ sudo rm -rf /usr/local/var/run/watchman
```
完了以后:
```bash
$ git clone https://github.com/facebook/watchman.git
$ cd watchman
$ git checkout v4.7.0
$ ./autogen.sh
$ ./configure
$ make
$ sudo make install
``
然后改下权限:
```bash
$ chmod 0700 /usr/local/var/run/watchman/<user>-state
```

目前我遇到的问题就是这些,后面会继续更新.....

资料:

[stackoverflow][1]


[react-native][2]


  [1]: http://stackoverflow.com/questions/40414166/react-native-npm-start-with-watchman-error
  [2]: https://github.com/facebook/react-native/issues/9943
