---
title: 小试Vuejs
date: 2017-02-27 09:20:55
categories:
  - 前端框架
tags:
  - JavaScript
---
　　近期工作量不是太多所以折腾了下 vuejs, 大体上把 vue 的文档看了一遍, 感觉好像遇到了 react 和 angular 的儿子似的, 这是一个小而美的框架, 它以极其简单且友好的方式解决了前端开发中 ViewModel 层面的问题，加上原来越完善的生态环境,让我感觉这个框架很值得学习和研究
　　刚好海尔需要一个无人机展示的 demo, 要求能在地图上显示无人机的各种信息比如: 禁飞区域, 实时航线的功能(要求能操作), 反正是 demo 展示所以直接用 vue 开撸了

# 开始
　　没有折腾太多, 直接用了 vue 官方的脚手架, 直接 init 了一把, 感觉还是蛮贴心的自己不用花时间去捣鼓开发环境
```bash
vue init webpack
```
完成各种依赖安装后就开始项目开发了, 这个 demo 我总共花费了大概5个小时左右, 线上地址大家可以看看效果: [无人机 demo](http://wurenji-demo-map.limijiaoyin.com/#/)

对于项目结构我这里就不多说了

# 写第一行 vue
　　其实之前写 angular 的时候就接触了模板这个概念, 那个时候 view 和 model 的数据绑定和渲染还是挺有印象的, vue 可以自定义模板或者 jsx 也可以, 但是推荐是直接用 html, 这才是写前端嘛! 相比react不同点在于:
* 模板: react 需要指定 template, vue 是直接写在 HTML 里面指定el 即可
* data: react 是通过get和set处理一堆数组相关的方法, vue 是直接操作 vue 的实例

目前我发现的就是这些
　　不过发现 vue 的事件修饰符我挺喜欢的而且还可以自定义,在事件处理程序中调用 event.preventDefault() 或 event.stopPropagation() 是非常常见的需求, 但是 vue 提供的这个功能简直太贴心了

好了不多说看我写的第一行 vue:
```javascript
<template>
  <div id="app">
    <router-view :map="BMap"></router-view>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      BMap: '',
    };
  },
  created() {
    if (window.BMap && BMap) {
      const map = new BMap.Map('planMap'); // 创建Map实例
      const point = new BMap.Point(116.404, 39.915); // 创建点坐标
      map.centerAndZoom(point, 15);
      this.BMap = map;
    }
  },
};
</script>
```
我这里没有做很多校验,直接是把百度地图的 API 在全局引用了, 然后把 map 的实例作为属性丢给了子组件
```javascript
import fly from '../assets/fly.png';
import land from '../assets/land.png';
import uav from '../assets/uav.png';

export default {
  name: 'going',
  props: {
    map: {
      default: '',
      type: Object,
    },
  },
  data() {
    return {
      lushu: null,
    };
  },
  methods: {
    init() {
      const map = this.map; // 地图实例
      map.enableScrollWheelZoom();
      map.clearOverlays();
      const startPoint = new BMap.Point(116.301934, 39.977552); // 起点
      const endPoint = new BMap.Point(116.508328, 39.919141); // 终点
      const startiIcon = new BMap.Icon(fly, new BMap.Size(32, 32)); // 起点图标
      const endIcon = new BMap.Icon(land, new BMap.Size(32, 32)); // 终点图标
      const startMarker = new BMap.Marker(startPoint, { icon: startiIcon });
      const endMarker = new BMap.Marker(endPoint, { icon: endIcon });
      map.addOverlay(startMarker);
      map.addOverlay(endMarker);
      const drv = new BMap.DrivingRoute(map, {
        onSearchComplete: (res) => {
          if (drv.getStatus() === window.BMAP_STATUS_SUCCESS) {
            const plan = res.getPlan(0);
            let arrPois = [];
            for (let j = 0; j < plan.getNumRoutes(); j += 1) {
              const route = plan.getRoute(j);
              arrPois = arrPois.concat(route.getPath());
            }
            map.addOverlay(new BMap.Polyline(arrPois, { strokeColor: '#337ab7' }));
            map.setViewport(arrPois);

            this.lushu = new BMapLib.LuShu(map, arrPois, {
              defaultContent: '',
              autoView: true, // 根据视野自动调整
              icon: new BMap.Icon(uav, new BMap.Size(32, 32)),
              speed: 1250,
              enableRotation: false, // 是否设置marker随着道路的走向进行旋转
              landmarkPois: [],
            });
          }
        },
      });
      // 开始运动
      drv.search(startPoint, endPoint);
      setTimeout(() => {
        this.lushu.start();
      }, 2000);
    },
    start() {
      this.lushu.start();
    },
    stop() {
      this.lushu.pause();
    },
  },
  created() {
    this.init();
  },
};
```
这样map 的实例就能很方便的在子组件中使用了, 我还记得使用ng时组件之间的数据通信简直是...

# 总结
是的, 你没有看错, 写了一行我就总结了
相比 react 这些来说我个人的看法是 vue 更适合做小而简的项目, 如果项目过于庞大还是使用 react 比较好
