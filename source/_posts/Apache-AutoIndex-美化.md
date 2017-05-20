title: "Apache AutoIndex 美化"
date: 2015-04-30 11:29:11
categories:
- 服务器
---

通过Apache搭建网页服务器，但开启了某个目录的autoindex功能时，可以很方便的实现文件共享。
但apache默认生成的界面看起来实在太丑。我们可以配置http.conf来配置生成界面的样式， 但如果你不了apache的配置，不了解html和css， 操作起来也是很不方便。

github上的一个开源项目Apaxy正好解决了这个问题。
    
[Apaxy](https://github.com/AdamWhitcroft/Apaxy)

Apaxy主要功能：


- 使用 CSS 美化目录浏览。
- 可以使用 JavaScript 或者 jQuery 方式弹窗。
- 可以添加欢迎消息，下载指示和版权信息。
- 添加自定义文件类型图标（需要修改 .htaccess 文件）。

具体使用方法，参考github上面的项目说明。

