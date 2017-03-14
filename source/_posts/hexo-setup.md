---
title: 使用 hexo 和 GitHubPage 搭建个人博客
date: 2014-06-16 14:24:14
categories:
  - 技术研究
tags:
  - 配置
---

　　[Hexo](https://hexo.io/zh-cn/) 是一个快速、简洁且高效的博客框架。之前我还用过 ghost 之类的博客构建系统, 感觉还是这款用起来比较顺手, 而且高度可配置;刚开始也会在 [CSDN](http://www.csdn.net/), [segmentfault](http://segmentfault.com/), [简书](http://www.jianshu.com/) 写博客,
　　但是一直想有一个自己的博客,奈何不想去折腾买服务器和域名(其实木有钱), 所以就借助 hexo 和 gitHub 搭建自己的个人博客.

## 1. 前 提
安装 Hexo
```bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server

```

这里自己看官方文档就好...

## 2. gitHub 创建项目
　　这里就要啰嗦下, GitHubPage是你项目的演示网址, 但是只能是静态的 html 页面不过刚刚好满足我们搭建自己的博客
* 首先登录你的 [gitHub](https://github.com)
* 然后创建一个项目
![项目创建](https://pages.github.com/images/user-repo@2x.png)

> 这里注意了 仔细看清楚了: repository name 这个输入框, 这个xxx.github.io 就是你以后博客的地址,
  xxx 是你的 github 用户名也就是你的登录名 不要搞错了 要不就访问不了
* 项目创建了好了 不用管它 来看下一步

## 3. 本地配置和预览
　　在本地用 hexo init 了以后就可以开始写博客了, 但是在写之前我们还需要配置下自己的博客, 下面是我的配置项(只列出我配置的其他的都是默认):
```yml
title: 这样生活 很美... ## 这是你博客的名称
subtitle:             ## 副标题
description:          ## 博客的描述
author: 钟 明          ## 博主
language: zh-Hans     ## 国际化 这里是中文
timezone:             ## 时间戳 可以不用管

url: https://zmGitHub.github.io ## 这就是你在 github 上创建的项目名
root: /  ## 资源访问起点
permalink: :year/:month/:day/:title/
permalink_defaults:

.....
.....
## https://hexo.io/themes/
theme: next ## 这里我用了 next 主题 看自己喜欢了

# 部署配置
## 文档: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/zmGitHub/zmGitHub.github.io.git
  branch: master

```

> 这里还是要啰嗦下, 我们在 github 上是可以去配置项目的GitHub Pages
有一个Automatic page generator 的功能这是项目的说明主页, 不要把自己的博客放到这个分支上

## 主题配置和插件使用

我这里是使用了 [Next](http://theme-next.iissnan.com/) 主题, 光 star 就有9000千多,
看自己喜好,主题很多, 他的配置文档写的非常详细我就不多说
然后我使用了这些插件:
* 多说
* 百度分析
* Google 分析
* 文章阅读数量统计


基本就是这样了关于 hexo 搭建的文章太多了, 另外 [markdown](http://daringfireball.net/projects/markdown/basics) 的语法

