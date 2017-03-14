---
title: ionic 旅途
date: 2015-04-22 21:17:40
tags:
  - JavaScript
  - ionic
  - App
---
## 在ionic中巧用iframe解决跳转到第三方平台时不能回调的问题－比如支付 ##


----------

　　在ionic开发中遇到不少的坑，绝大部分解决了但是在我们的app中如果跳转到第三方网站上时，那么问题来了．此时我们的app是不能监听到你在其它网站上的事件的，所以当你想要回退到我们自己app上时,请紧握你的蛋,小编在开发这个模块时就遇到了很大的麻烦想尽各种办法还是不能解决,下面我就跟大家讨论下我在ionic中遇到的问题:

----------

 *在开发一款机票砍价的ionic app中用户可以选择去携程订票,这个时候问题来了因为我监听了android的back的事件,而且这个事件是cordovaJS调用了原生的事件来进行处理的.代码如下:
```javascript
$ionicPlatform.registerBackButtonAction(function (e) {
  e.preventDefault();
  ......
  ......
  return false;
}, 101);
```
　　但是当我跳转到携程的时候,这个监听函数是不会起作用的,所以我就凌乱........,当用户要返回到我的App时要不停的按手机的返回键,这个时候就相对于浏览器的后退一样了,这个时候就会出现一个不好的表现了,回退的功能会把你浏览过的route全部重新访问一边之后才会退出我们的应用,如果没有使用上面的registerBackButtonAction的话,这是在android上,至少是可以回退,但是在ios上呢?这个就会很逆天了,会被别人骂的要死......
 *另一个问题是:用户支付的时候,我们为了方便使用了网页版的支付方式,同样的问题同样的味道,虽然可以自己写cordva的插件.但是毕竟这个还是比较费时间的,如果小伙伴需要这样的功能我推荐大家用这个: [pingpp](https://github.com/TongChia/cordova-plugin-pingpp)


最后的最后实在是没办法了......
想到了用iframe现在才想到,唉~


不瞎扯分享下我的经验:

首先使用$ionicModal创建一个弹出层可以覆盖整个页面,具体就看大家的业务需求了
在modal中加入如下的代码:

```javascript
<ion-modal-view>
    <ion-header-bar align-title="center" class="bar-positive">
        <div class="buttons">
            <button ng-click="vm.cancelPay()" class="button button-clear button-icon icon ion-ios-arrow-back"></button>
        </div>
        <h1 class="title">支付宝</h1>
    </ion-header-bar>
    <ion-content scroll="true" class="has-header no-padding">
        <iframe data-tap-disabled="true" ng-src="{{vm.paySrc}}"></iframe>
    </ion-content>
</ion-modal-view>
```

注意下我们需要配置ion-content的**scroll="true" class="has-header no-padding"** 和iframe的**data-tap-disabled="true"**
这里就是可以解决用户不能操作iframe里面的东西

　　其中如果想要动态的改变iframe的src一定要用**ng-src="{{vm.paySrc}}"**但是这里就需要注意了,如果你的**vm.paySrc**是普通的链接是不能访问的,因为Angularjs 中防止了用户注入URL,**所以解决办法是使用 $sce.trustAsResourceUrl(URL)**

```javascript
vm.paySrc =  $sce.trustAsResourceUrl('http://www.baidu.com/'); //URL 为全链接（$sce.trustAsResourceUrl("http://" + url)）
```

最后就是添加css了:

```css
.scroll-content > .scroll {
    height: 100%;
}
.scroll-content > .scroll > iframe {
    min-width: 100%;
    min-height: 100%;
}

```

好了基本上就是这样了其它的细节大家去优化吧!这里是参考的资料:

https://github.com/driftyco/ionic/issues/1151

http://forum.ionicframework.com/t/fill-content-container/605

http://www.erpchn.com/diannao/43242.html

http://www.thinksaas.cn/group/topic/350702/
