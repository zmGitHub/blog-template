---
title: ubuntu14.10创建应用快捷方式
date: 2015-07-05 15:57:24
categories:
  - 技巧积累
tags:
  - Linux
---
在ubuntu下使用webStorm，phpStorm,IDEA等编辑器的时候，不能像windows下那么方便的创建快捷方式，所以和大家分享下我在ubuntu下是如何创建应用的快捷方式的;

1. 首先在你安装应用的目录下创建一个新的文本(其实可以随便在任何一个地方创建)，然后在添加如下内容：
```
[Desktop Entry]
Categories=Development;
Comment[zh_CN]=
Comment=
Exec=/opt/WebStorm/bin/webstorm.sh 　　####这里是大家需要注意的地方.和你自己的相对应
GenericName[zh_CN]=IDE
GenericName=IDE
Icon=/opt/WebStorm/bin/webide.png　　　####这里是大家需要注意的地方.和你自己的相对应
Name[zh_CN]=webStorm　　　　　　　　　　　####应用的中文名字，可以随便取
Name=webStorm　　　　　　　　　　　　　　　####应用的英文名字，可以随便取
Path=
StartupNotify=true
Terminal=false
Type=Application
X-DBUS-ServiceName=
X-DBUS-StartupType=
X-KDE-SubstituteUID=false
X-KDE-Username=owen
```

**大家改好后请将我后面的注释去掉**

2.保存编辑．将该文件的后缀名改为 desktop,比如：**webStorm.desktop**

右击文件，找到权限，如下图：
{% asset_img 1557127733-5599384d0b3ed.png %}


点击关闭．就ok了，然后复制到桌面就可以了，其它的应用也是如此......
