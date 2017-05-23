---
title: "AndroidStudio代理设置"
date: 2015-05-06 09:57:42
categories:
- android
---

Android Studio 第一次启动时会检测Android SDK的信息，无奈有GFW的阻挡，即使翻墙，下载速度也让人抓狂，还好国内有几个不错的镜像网站。

## 禁用第一次启动时检测SDK信息

打开Android Studio之前，找到Android Studio的配置文件idea.properties, 在android-studio/bin目录下，在这个文件的最后一行*添加*一行：

>disable.android.first.run=true

然后打开Android Studio, 进入Configure->SDK Manager, 设置代理。设置完成后关闭Android Studio。

如果想修改Android Studio的代理，进入Configure->Settings->HTTP Proxy settings。

再*删掉*刚才添加的那一行，保存后再重新打开Android Studio。

## 修改Android Studio代理

如果Android Studio已经运行过，即使是第一次运行失败，会生成Android Studio的配置文件夹，UNIX系统下路径为：~/.AndroidStudio/, 找到config/options/other.xml, 对下面几行进行设置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<application>
  <component name="HttpConfigurable">
    <option name="USE_HTTP_PROXY" value="true" />
    <option name="PROXY_HOST" value="127.0.0.1" />
    <option name="PROXY_PORT" value="3128" />
  </component>
</application>
```


