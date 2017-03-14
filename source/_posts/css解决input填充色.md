---
title: css解决input填充色
date: 2014-07-23 16:27:11
categories:
  - 技巧积累
tags:
  - css
---

最近开发的时候发现 form 表单添加了自动填充后,在 Google 浏览器上有黄色的背景, 尝试过下面的代码去解决然而发现并没有什么用:
```css
input[type="text"]:-webkit-autofill {
  background-color: '#fff'
}
```
查询资料发现直接覆盖是没有用, 即使你用了 important 还是没有用, 在 stackoverflow 上还是有个背景好的 hack 可以使用:
```css
input:-webkit-autofill,
textarea:-webkit-autofill,
select:-webkit-autofill {
    -webkit-box-shadow: 0 0 0 1000px white inset;
}
## 还有这个
/*清除input框的自动填充颜色*/
input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus,
input:-webkit-autofill:active {
    transition: background-color 5000s ease-in-out 0s;
}
```
