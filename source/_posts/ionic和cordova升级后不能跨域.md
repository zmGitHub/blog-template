---
title: ionic和cordova升级后不能跨域
date: 2015-05-12 21:17:40
tags:
  - JavaScript
  - ionic
  - App
---
今天升级了ionic和cordova后发现以前能跨域的请求现在都不能了，升级的方法如下：

```bash
bower update ###更新ionic的css,js等

sudo npm update -g cordova ###更新cordova　到5.1.1 我的ionic 是1.5.5


cordova platform update android ###更新cordova.js

bower clean cache　###清理下缓存

```

执行完上面的代码，在浏览器里面是没问题的，但是当我去build的时候就不得了，直接报错说我没有安装android sdk api 22　我更想api的时候又遇到坑了，因为我们大天朝我一直更新不了,在网上一番搜索后得到以下的解决办法:

 1. 我常用的翻墙方法是改[hosts][1]文件(我是ubuntu其实windows也一样)，


 2. 但是这样我们还是不能更新api，肿么办？改android-sdk-manager的下tool>option:

```
Proxy Settings
    HTTP Proxy Serve => mirrors.opencas.cn
    HTTP Proxy Port  => 80

Force https ://...source tobe fetched using http:///... ###勾选这项

Enable Preview Tools  ... ###勾选这项
```
其实是改了下代理而已，我们还是要学会使用镜像,这里面有很多的[代理镜像][2]


现在解决了打包是不会报错了，但是安装apk 以后发现我连登录都不行了这下好玩了，后来到ionic 的论坛里面去瞎看无意间看到很多人在讨论 如何点击链接拨打电话：
```html
<a class="tab-item" href="tel:0039340123456">call me</a>
<a class="tab-item"  ng-href="tel:{{vm.article.tel}}">call me</a>
```
刚开始还不知道是什么情况，后来无意间看到他们说必须加上白名单才能用，我想是不是这个原因，后来查了资料发现cordova5.0版本以后的为了安全都禁止了发送请求,很多的东西我们必须自己配置:
```bash
cordova plugins add cordova-plugin-whitelist ###安装白名单插件

```
然后在我们app的config.xml中配置:
```xml
    <content src="index.html"/>
    <access origin="*"/>
    <access origin="tel:*" launch-external="yes"/> ###允许调用外部电话
```

具体的请看[白名单的API][3],这下我们可以不用jsonp了，也不用担心跨域了


  [1]: http://levi.cg.am/archives/3553
  [2]: http://www.androiddevtools.cn/
  [3]: https://github.com/apache/cordova-plugin-whitelist
