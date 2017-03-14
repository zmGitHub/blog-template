---
title: JavaScript奇淫技巧
date: 2016-10-08 09:47:43
categories:
  - 技巧积累
tags:
  - JavaScript
---

## 1.数字处理
``` JavaScript
// 向下取整
let num1 = ~~1.2; // 1
let num2 = 3.6>>0; // 3
let num3 = 10.67345|0; // 10
let num4 = 13.66454^0; // 13

// 字符串转数字
let str = +'1231'; //1231 但是1231dsf 这样的不行 会是 NaN

// 验证是否是数字:
function isNumber(n){
    return !isNaN(parseFloat(n)) && isFinite(n);
}

// 浮点计算的问题
0.1 + 0.2 === 0.3 // is false
9007199254740992 + 1 // is equal to 9007199254740992
9007199254740992 + 2 // is equal to 9007199254740994
/* 因为0.1+0.2等于0.30000000000000004。JavaScript的数字都遵循IEEE 754标准构建，在内部都是64位浮点小数表示
可以通过使用toFixed()和toPrecision()来解决这个问题。*/
```

## 2.日期转数字
``` JavaScript
let num = + new Date();
```

## 3.随机码
``` JavaScript
Math.random().toString(16).substring(2) // 14 位
Math.random().toString(36).substring(2); // 11 位

// 获取数组的随机元素
let items = [12, 548 , 'a' , 2 , 5478 , 'foo' , 8852, , 'Doe' , 2145 , 119];
items[Math.random()*items.length|0] // items[Math.floor(Math.random() * items.length)]

// 获取指定范围内的随机数
(Math.random()*(max-min+1))|0 + min; // Math.floor(Math.random() * (max - min + 1)) + min;
```

## 4.条件判断
``` JavaScript
let a = b && 1;
//相当于:
if (b) {
  a = 1;
}else {
  a = b;
}

let a = b || 1;
//相当于
if (b) {
  a = b;
} else {
  a = 1;
}

underfined、null、0、false、NaN、空字符串的逻辑结果均为false
```

## 5.判断 IE
``` JavaScript
let ie = /*@cc_on !@*/false;
let ie8 = document.documentMode==8 && !!window.ActiveXObject;
```

## 数组处理
``` JavaScript
let arr = [12 , 222 , 1000 ];

验证是否是数组:
function isArray(obj){
    return Object.prototype.toString.call(obj) === '[object Array]' ;
}

求数组的最大值和最小值
Math.max.apply(null, [1,3,5,8]) // 8  IE8 会出错 (Math.min.apply(null, [1,3,5,8]))
或者
let arr = [3,5,79,4];
Math.max(...arr); // 79 Math.min(...arr);

清空数组
arr.length = 0;
```

## 参考资料
[JSTips](http://www.jstips.co/)