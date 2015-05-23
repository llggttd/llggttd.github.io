title: "Mac下弹出气泡通知"
date: 2015-05-23 18:03:25
updated: 2015-05-23 18:03:25
categories:
tags:
---

在Mac上，有时需要将比较耗时的工作放在terminal下执行，我们希望程序执行完成后能通知我们。Mac的通知中心做的很不错，我们可以借助通知中心来通知我们。但苹果没有提供termial下操作通知中心的工具，我们可以借助工具 terminal-notifier来实现。


## 下载terminal-notifier

    [terminal-notifier](https://github.com/alloy/terminal-notifier)

    安装方法参考github上的项目说明文档

## 使用terminal-notifier

terminal-notifier -[message|list|remove] [VALUE|ID|ID] [options]

- title VALUE       通知的标题，默认为“Terminal”
- subtitle VALUE    副标题
- sound NAME        通知出现时播放的声音，使用"default"来使用系统默认声音，声音名为"Preferences"-"Sound"
- group ID         字符，消息的组名
- activate ID       指定点击通知时打开的应用的bundle
- sender ID         指定发送通知的应用的bundle
- appIcon URL       指定通知上显示的图标
- contentImage URL  指定通知内消息的图片
- open URL          指定点击通知时打开的链接
- execute COMMAND   指定点击通知时执行的命令

我写的一个打开pdf文件的脚本

``` bash
#!/bin/sh

#通过launchctl启动脚本，环境变量只有系统路径
echo `date +%H:%M\ %D`
PATH="$PATH:/usr/local/bin"
CMD="open -a /Applications/Skim.app /Users/uniflor/Documents/文档/Linux\ Kernel\ Development.pdf"

#显示通知，第一个参数为消息的内容，第二个参数为消息的标题
notify(){
    terminal-notifier -message "$1" -title "$2" -appIcon /Users/uniflor/Bin/notification.png -execute "$3"
}

notify "Linux Kernel Development" "通知" "$CMD"
```

