title: "Mac下添加定时任务"
date: 2015-04-29 15:00:00
updated:
layout: post
comments: true
categories:
- Mac
tags:
---

## 步骤

- 编写任务脚本

把想要执行的工作写入脚本文件，如添加文件run.sh, 写入以下内容：

```sh
#!/bin/bash
echo `date` > $HOME/log
```

- 编写任务描述文件

mac下的任务描述文件是plist格式的。结构如下：

``` xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">  
<plist version="1.0">  
  <dict>
    <!-- 名称，要全局唯一 -->
    <key>Label</key>
    <string>com.uniflor.notifier</string>

    <!-- 要运行的程序， 如果省略这个选项，会把ProgramArguments的第一个
    元素作为要运行的程序 -->
    <key>Program</key>
    <string>/Users/uniflor/script.sh</string>

    <!-- 命令， 第一个为命令，其它为参数-->
    <key>ProgramArguments</key>
    <array>
      <string>/Users/uniflor/script.sh</string>
    </array>

    <!-- 运行时间 -->
    <key>StartCalendarInterval</key>
    <dict>

      <key>Minute</key>
      <integer>30</integer>

      <key>Hour</key>
      <integer>9</integer>

      <key>Day</key>
      <integer>1</integer>

      <key>Month</key>
      <integer>5</integer>

      <!-- 0和7都指星期天 -->
      <key>Weekday</key>
      <integer>0</integer>

    </dict>

    <!-- 运行间隔，与StartCalenderInterval使用其一，单位为秒 -->
    <key>StartInterval</key>
    <integer>30</integer>

    <!-- 标准输入文件 -->
    <key>StandardInPath</key>
    <string>/Users/uniflor/run-in.log</string>

    <!-- 标准输出文件 -->
    <key>StandardOutPath</key>
    <string>/Users/uniflor/Bin/run-out.log</string>

    <!-- 标准错误输出文件 -->
    <key>StandardErrorPath</key>
    <string>/Users/uniflor/Bin/run-err.log</string>

  </dict>  
</plist>
```


两种指定要执行命令的方法：

  + 使用Program和ProgramArguments

    *Program*, 运行命令或要执行文件路径
    *ProgramArguments*, 执行时传入参数

  + 只使用ProgramArguments

    此时，ProgramArguments的每个参数为要执行的命令或文件路径，其它参数为传入参数

两种设置执行时间的方法：

  + StartCalendarInterval

    使用元素Minute, Hour, Day, Month, Weekday两指定执行时间，如：

    ``` xml
    <key>StartCalendarInterval</key>
    <dict>
      <key>Minute</key>
      <integer>30</integer>
      <key>Hour</key>
      <integer>9</integer>
    </dict>
    ```

  每天的9:30执行

  + StartInterval
  
  设置执行的时间间隔，单位为秒

  ``` xml
  <key>StartInterval</key>
  <integer>3600</integer>
  ```

  每小时执行一次

- 使用launchctl加载plist

  任务描述文件写好后，怎么使用它呢？可以使用launchctl工具，添加任务到launchd。

  系统定义了几个位置来存放任务列表
  + ~/Library/LaunchAgents         由用户自己定义的任务项
  +  /Library/LaunchAgents         由管理员为用户定义的任务项
  + /Library/LaunchDaemons         由管理员定义的守护进程任务项
  + /System/Library/LaunchAgents   由Mac OS X为用户定义的任务项
  + /System/Library/LaunchDaemons  由Mac OS X定义的守护进程任务项

  *为用户定义的任务项，只以用户登陆后才会执行，守护进程任务项无论用户是否登陆都会执行*
  根据添加的任务类型，把它拷贝到相应的目录，通过launchctl来加载：

  + 添加 launchctl load /System/Library/LaunchAgents/com.test.plist
  + 移除 launchctl unload /System/Library/LaunchAgents/com.test.plist
  + 查看 launchctl list
