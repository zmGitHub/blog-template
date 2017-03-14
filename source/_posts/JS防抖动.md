---
title: JS防抖动
date: 2016-03-11 15:29:25
categories:
  - 技巧积累
tags:
  - JavaScript
---
　　目前遇到的情况是, 现在还是使用公司的框架在表单处理非常的复杂, 常常出现表单远程校验的问题, 这个时候就遇到了频繁查询的问题, 因为目前的做法是 input 的 change 过后就去查询后台的接口这导致非常多的性能损耗和服务器不必要的压力, 在开发过程中了我用了如下的解决方案:
> 明白了防抖动的原理其实就是: 延迟处理, 所以我在开发中的是这样解决的:
```javascript
event.on('change', function(e){
    var fun;
    return function(){
        if(fun) clearTimeout(fun);
        fun = setTimeout(function(){
            // 我们的异步处理函数
        },500);
    }
}());
```
这种解决方案是把函数放在了定时器中, 当然我们可以把这个函数当成一个公共的方法:
```javascript
function swall(something,delay){
    var fun;
    return function(){
        if(fun) clearTimeout(fun);
        fun = setTimeout(function(){
            something();
        },delay);
    }
}

# 比如滚动的时候处理方法
event.on('scroll', swall(func,1000))
event.on('scroll', swall(func,2000))
```
　　我大概还看了一下其他的解决方案就是节点流的处理方式, 我对于他们的理解是我们平时坐电梯什么的就比较像, 比如人多的时候有人进来电梯开始计时如果中途有人按了门铃那电梯开始重新计算直达没有人按了就开始运行, 函数节流就比如是 VIP 电梯老板来了 那就把老板送到楼上再下来接其他人, 不管中途有没有人按电梯(呃! 感觉好像很欠打的样子....)
