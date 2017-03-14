---
title: eslint airbnb 初始化
date: 2016-06-13 16:15:30
categories:
  - 技巧积累
tags:
  - JavaScript
---
　　现在我们的代码规范都是使用了 eslint 去规范我们前端代码的开发, 但是每次使用 eslint --init 之后 经常出现各种依赖不协调的警告, 看着挺蛋疼作为一个有追求的前端来说, 具体的解决办法是:
```bash
(
  export PKG=eslint-config-airbnb;
  npm info "$PKG@latest" peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs npm install --save-dev "$PKG@latest"
)
```
　　其实原理很简单就是分析出 eslint 下各种依赖的最新包然后安装它是不是很简单呢! 只要把上面的命令在命令行中 copy 一下就 OK 了
