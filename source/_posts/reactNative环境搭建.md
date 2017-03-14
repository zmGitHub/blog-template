---
title: reactNative Android 环境搭建
date: 2016-12-09 17:01:44
categories:
  - 技术研究
tags:
  - react
  - reactNative
  - App
---
- - - -
## Android 环境搭建:
下载 Android studio:
[android studio](https://developer.android.com/studio/index.html#tos-header)
下载完成后选择 custom:
设置代理:
```
proxy: android-mirror.bugly.qq.com 8080
```
默认的 sdk 位置:
```
/Users/turbo/Library/Android/sdk
```
如果安装失败: 提示 :
```
SDK tools directory is missing
```
### 解决方案:
卸载重新安装(直接卸载, 如果卸载后配置文件还是在就使用下列命令)
```
rm -Rf /Applications/Android\ Studio.app

rm -Rf ~/Library/Preferences/AndroidStudio*

rm ~/Library/Preferences/com.google.android.studio.plist

rm -Rf ~/Library/Application\ Support/AndroidStudio*

rm -Rf ~/Library/Logs/AndroidStudio*

rm -Rf ~/Library/Caches/AndroidStudio*

rm -Rf ~/AndroidStudioProjects

rm -Rf ~/.gradle

rm -Rf ~/.android

rm -Rf ~/Library/Android*

```

如果重新安装还是不行, 直接下载 sdk
[SDK](https://developer.android.com/studio/index.html#tos-header) 选择仅获取命令行工具

## 环境变量配置:
配置用户目录下的: ~/.bash_profile 不是 ~/.bashrc, 然后在行尾加入如下代码:
```
# Android sdk
export ANDROID_HOME=~/Library/Android/sdk
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools
```

### 配置完成后更新配置:
```bash
# 在用户目录下操作
cd ~
> source .bash_profile
> android # 执行后会弹出 sdk manager
```

### 选择对应的 Android API 和构建工具:
{% asset_img 5A46CEAD-8D64-46A9-84C0-385916B44E4C.png %}
{% asset_img D22059F1-3806-44A8-B09A-F62A19A06BFE.png %}

这里可能会出现有的包显示不了, 所以你需要配置下[Android repository]( [Android development tools mirror.](http://android-mirror.bugly.qq.com:8080/include/usage.html)
```
http://android-mirror.bugly.qq.com:8080/android/repository/addon.xml
```
位置是: Android SDK Manager > Tools > Add-on Sites > User Defined Sites
{% asset_img FA1ED908-7EED-4134-9D01-B28F2F52C196.png %}
然后再重新选择: Packages > reload 会发现没有的包出来了

选择后开始安装就好了

## 安装 gradle
```bash
> brew install gradle
# 开启Gradle Daemon可以极大地提升java代码的增量编译速度。

> touch ~/.gradle/gradle.properties && echo "org.gradle.daemon=true" >> ~/.gradle/
gradle.properties

# gradle 安装完成后会在用户目录生成.gradle 文件夹
```

但是还有个问题, react-native run-android 的时候回特别的慢, 我开着 vpn 可能是 vpn 不好的原因下载不了, 所以我们解决下:

```bash
# 找到你项目下:
android
--gradle
----wrapper
------gradle-wrapper.properties # 编辑这个文件
```

更改distributionUrl的值, 因为他是从默认的网站下载, 这里大家自己下载下 gradle 的包, 然后在下载目录启动 : http-server ./ -p 9000 这里大家可以用其它方式,只要能下载下来就好了, 你会发现非常快:

```bash
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=http\://localhost:9000/gradle-2.4-all.zip
```
## 安装 Genymotion, 得先按照 Virtual box
新建虚拟机也是非常慢的, 基本下载个虚拟机得好久好久 , 快要好的时候就挂了, 当然如果你网速好的话请忽略这个问题, 那我们的解决办法是:
```bash
# 进入你用户主目录
> cd ~.Genymobile
# 在目录下回发现 genymotion.log 这个文件打开它, 找到下载虚拟机的连接
# 手动下载这个文件
# 下载完成后, 把虚拟机的文件 copy 到:
> cd .Genymobile/Genymotion/ova
# 复制完成后, 重新在 Genymotion里 add 你下载的机器,就 OK 了
```
- - - -
然后你就可以愉快的运行了, 但是可能还会发现大姨妈(红红的一片), 这个问题首先先把虚拟机的开发者模式打开: 设置->关于手机>->不停的点击版本号;
如果还是不行, 就把虚拟机重新安装就好了;

 如果运行 react-native run-android 后出现: connect devices time out, 首先检查模拟器的有没有连接上来:
```bash
> adb devices
# 输出如下信息表示已经连接上了
List of devices attached
192.168.56.101:5555	device
```

有上面的连接但是还是连接模拟器超时, 请配置 Genymotion 的 Android sdk:
{% asset_img 48E8706F-7826-4177-A4C6-546AD2164DD8.png %}

这里不要使用 genymotion 的默认 sdk 改为我们的即可, 然后重新启动下模拟器, 就 OK 了

如果出现不能连接服务器的大姨妈红请确保你的模拟器连接上了网络

## 开发规范
初始化 eslint:
```bash
(
  export PKG=eslint-config-airbnb;
  npm info "$PKG@latest" peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs npm install --save-dev "$PKG@latest"
)
```
.eslintrc 配置
```json
{
  "extends": "airbnb",
  "parser": "babel-eslint",
  "installedESLint": true,
  "plugins": [
    "react",
    "import"
  ],
  "env": {
    "browser": true,
    "node": true,
    "es6": true
  },
  "rules": {
    "react/jsx-filename-extension": 0,
    "indent": [2, 2, {
      "SwitchCase": 1
    }]
  },
  "globals": {
    "fetch": true
  },
  "settings": {
    "ecmascript": 6,
    "jsx": true
  }
}
```
